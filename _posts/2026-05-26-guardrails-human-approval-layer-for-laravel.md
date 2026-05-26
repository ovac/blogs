---
layout: post
current: post
cover: https://www.ovac4u.com/guardrails/img/social-card.svg
navigation: True
title: "Guardrails: an asynchronous human-in-the-middle layer for Laravel"
date: 2026-05-26 03:10:00
tags: [tech, laravel]
class: post-template
subclass: 'post tag-tech tag-laravel'
author: ovac4u
---

Some application actions are too important to be applied the moment they are requested.

Publishing a post is not the same as saving a draft. Changing a payout account is not the same as updating a display name. Raising a withdrawal limit is not the same as editing a preference. Disabling a user, changing a feature flag, approving a refund, modifying a local exchange rate, moving money, changing policy text - these are not just CRUD operations.

They are operational decisions.

[Guardrails](https://github.com/ovac/guardrails) is a Laravel package for those decisions. It is an asynchronous human-in-the-middle layer that captures a proposed change, routes it through the right reviewers, records signatures, and applies the update only after the required approval flow is satisfied.

The package exists because "only admins can do it" is not enough, and because not every important change starts with a human clicking a button.

Admins make mistakes. Operators work under pressure. A tired engineer can change the wrong flag. A support lead can approve the wrong refund. A founder can move too quickly. But the same risk also shows up in automated jobs, third-party webhooks, AI agents, imports, integrations, and internal tools that act faster than a team can manually supervise.

The goal is not to remove automation from the system. The goal is to put accountable human review in the middle of the actions where judgment still matters.

## Approval is business logic

Many applications hide approvals in places where they become hard to reason about.

Sometimes approvals live in a Slack thread. Sometimes they live in a spreadsheet. Sometimes they are buried in a controller method:

```php
if ($user->isAdmin()) {
    $order->status = 'refunded';
    $order->save();
}
```

That code answers one question: can this user perform the action?

It does not answer the better questions:

- Should this action pause for review?
- Who should review it?
- Does the initiator count as one signer?
- Is one approval enough, or do we need two?
- Should Finance approve before Security?
- What original value did we replace?
- Who signed, when, and why?
- What event should notify the rest of the system?

Those questions are business logic. They deserve first-class code.

Guardrails makes that approval logic explicit.

## The core model: capture, wait, sign, apply

The package follows a simple lifecycle.

1. A guarded change is attempted.
2. Guardrails captures the proposed mutation and the original values.
3. It creates an approval request with one or more approval steps.
4. The original request can return while the approval continues asynchronously.
5. Eligible signers approve or reject each step.
6. When the flow is satisfied, Guardrails applies the captured change.
7. Events fire so the application can notify people, audit, or integrate elsewhere.

The public package ships with migrations for approval requests, approval steps, and approval signatures. That data model is important. It means the approval is not a side conversation. It is application state.

That state can be queried, displayed, audited, tested, and connected to the rest of the system.

That asynchronous model is the point. A webhook can propose a change without being allowed to finalize it. A scheduled job can prepare a payout update without forcing the payout to move immediately. An AI assistant can draft an operational action without being trusted as the final authority. A staff user can submit a sensitive update without needing every reviewer online in the same request cycle.

Guardrails lets the application say: capture the intent now, gather the right signatures, then apply it later.

## Use it on models or at the controller edge

Guardrails supports two natural entry points.

The first is model-level guarding. Add the trait, declare the attributes that need review, and define the approval flow.

```php
use OVAC\Guardrails\Concerns\Guardrail;
use OVAC\Guardrails\Services\Flow;

class Post extends Model
{
    use Guardrail;

    public function guardrailAttributes(): array
    {
        return ['published'];
    }

    public function guardrailApprovalDescription(array $dirty, string $event): string
    {
        return 'Publish flag changes require editorial approval.';
    }

    public function guardrailApprovalFlow(array $dirty, string $event): array
    {
        return [
            Flow::make()
                ->anyOfPermissions(['content.publish'])
                ->includeInitiator(true, true)
                ->signedBy(2, 'Editorial Review')
                ->build(),
        ];
    }
}
```

That is a two-man rule for publishing: the author can count as one approval, but another eligible editor must sign before the change lands.

The second entry point is controller interception. That is useful when an action comes from an HTTP workflow, an admin tool, an integration, or an external system and you do not want to put all of the approval logic inside the model.

```php
$result = $this->guardrailIntercept($post, ['published' => true], [
    'description' => 'Editorial approval required before publishing.',
    'only' => ['published'],
    'extender' => Flow::make()
        ->anyOfRoles(['editor', 'managing_editor'])
        ->signedBy(1, 'Editorial Approval'),
]);
```

Both approaches matter. Some rules belong close to the domain model. Some rules belong at the edge of the request. Some rules sit between your application and something that wants to act on its behalf. A good approvals system should support all of those entry points.

## Flows should read like policy

The best part of Guardrails is the flow builder.

Approval code should be readable by the people who own the policy. If a product manager, operations lead, or security reviewer cannot understand the shape of the flow, the code is too clever.

Guardrails flows are intentionally plain:

```php
Flow::make()
    ->anyOfPermissions(['ops.change'])
    ->includeInitiator(true, true)
    ->signedBy(2, 'Ops Review')
    ->anyOfRoles(['cto', 'cfo'])
    ->signedBy(1, 'Executive Sign-off')
    ->build();
```

That says what it means:

- Ops starts the review.
- The initiator can be counted.
- Two ops-level signatures are required.
- Then one executive signature is required.

The package also supports role-based and permission-based signers, any-of or all-of matching, initiator rules, same-permission or same-role constraints, rejection thresholds, configurable controller flows, and multi-step escalation.

That gives you enough vocabulary to model real company rules without turning approval logic into a separate workflow platform.

## Real use cases

Guardrails is not only for one kind of application.

In a content system, it can require author plus editor approval before a post goes live.

In a sales system, it can route discounts below 20 percent to a sales lead and discounts above that to a VP.

In finance, it can require two signatures before a payout is released.

In engineering, it can pause feature flag changes until operations and an engineering lead approve rollout.

In compliance, it can require legal or security sign-off before policy text changes.

In AI-assisted software, it can let an agent propose a production change while requiring a human signer before the system actually mutates state.

In integration-heavy products, it can hold third-party webhook updates, imported records, partner actions, and scheduled automation until the right internal reviewer signs off.

In a crypto or fintech product, the same pattern applies to withdrawal limits, payment route configuration, settlement details, and anything else where "just save it" is too weak.

The package includes a reviewer UI, API endpoints, events, docs, a flow-builder playground, and tests. That matters because approval systems are operational software. They need to be visible, scriptable, and testable.

## Why this belongs in Laravel

Laravel teams already model business operations in Eloquent models, controllers, policies, events, queues, notifications, and tests.

Approvals should fit into that ecosystem.

Guardrails uses Laravel conventions instead of asking teams to adopt a separate workflow engine for a common class of problems. It works with the application auth layer, supports Spatie permissions or token abilities, publishes migrations and views, and lets the application keep ownership of the final policy.

That is the right tradeoff for many products. You get enough structure to prevent important changes from being applied silently, without moving your whole operational model outside the app.

## The principle

Guardrails is built around one principle:

Important changes should be proposed before they are applied.

That one shift changes the posture of an application. It creates time to review. It creates a record. It gives events somewhere to attach. It lets async workflows pause without losing context. It lets teams encode their operational maturity in code instead of relying on memory.

The older an application gets, the more important this becomes. New systems can survive on trust and speed for a while. Serious systems need traceability. They need review paths. They need a way to say, "this action can be proposed by software, but a responsible human still has to stand in the middle before it lands."

That is what Guardrails gives Laravel teams.

Repository: [github.com/ovac/guardrails](https://github.com/ovac/guardrails)

Docs: [ovac4u.com/guardrails](https://www.ovac4u.com/guardrails/)
