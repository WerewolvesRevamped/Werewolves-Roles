# A guide on how to formalize roles/groups/teams/attributes

## Contents

- [Basics](#basics)
- [Role](#role)
- [Abilities](#abilities)
  - [Ability](#ability)
    - [Trigger Types](#trigger-types)
    - [Action Restrictions](#action-restrictions)
    - [Action Scaling](#action-scaling)
    - [Action Compulsion](#action-compulsion)
    - [Compound Actions](#compound-actions)
    - [Complex Actions](#complex-actions)
  - [Target Types](#target-types)
  - [Duration Types](#duration-types)
  - [Ability Type](#ability-type)
    - [Killing](#killing)
    - [Investigating](#investigating)
    - [Targeting](#targeting)
    - [Disguising](#disguising)
    - [Protecting](#protecting)
    - [Applying](#applying)
    - [Redirecting](#redirecting)
    - [Vote Manipulating](#vote-manipulating)
    - [Whispering](#whispering)
    - [Joining](#joining)
    - [Granting](#granting)
    - [Loyalty](#loyalty)
    - [Obstructing](#obstructing)
    - [Poll Manipulating](#poll-manipulating)
    - [Announcements](#announcements)

## Basics
- `<Argument>`: Should be replaced by something. E.g. `<Argument>` ⇒ `Example`  
- `[<Argument1> | <Argument2>]`: Should be replaced by one of the options within `[]` which are separated by `|`, e.g. `[<Argument1> | <Argument2>]` ⇒ `<Argument1>` ⇒ `Example1`  
- `[<Argument1> | Text1 | Text2]`: Should be replaced by one of the options within `[]`, e.g. `[<Argument1> | Text1 | Text2]` ⇒ `Text1`
- `[<Argument>]`: Should be replaced either by `<Argument>` or removed entirely

Due to technical limitations if you see `'`'s within `this text` consider them to be \`'s.

## Role

```
<Role Name> | <Role Group> <Role Category> <Role Team>
[<Role Ability List> | No Abilities]
```
- Role Name: May be anything  
- Role Group: May be `Townsfolk`, `Werewolf`, `Solo`, `Unaligned` or `Extra`  
- Role Category: May be `Elected`, `Align`, `Recruitment`, `Killing`, `Group`, `Investigative`, `Power` or `Miscellaneous`  
- Role Team: Should be kept blank for any role group besides `Solo`, should be set to `- <Team Name>`, where `<Team Name>` is the name of a defined team  

- Role Ability List: A newline separated list of `Abilities`

## Abilities

----
----
### Ability
Single Ability
```
<Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
```
Multiple Abilities
```
<Trigger Type>:
  • <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
  • <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
```
The three elements `[<Action Restriction>]`, `{<Action Compulsion>}` and `<<Action Scaling>>` should be left out if unnecessary.

If a single trigger does several abilities at once, they can be listed as part of one trigger as shown above.

- Trigger Types: Specifies when the ability is used. See [here](#trigger-types)
- Action Restrictions: Specifies if there are restrictions on the action. See [here](#action-restrictions)
- Action Scaling: Specifies if the amount of actions scales with player count. See [here](#action-scaling)
- Action Compulsion: Specifies if the ability is forced. See [here](#action-compulsion)
- Ability Type: Specifies which ability type and how to execute it. See [here](#ability-types)

----
#### Trigger Types

Trigger types can be one of the following:
- An Action Timing (`Start Night`, `End Night`, `Start Day`, `End Day`, `Immediate Night`, `Immediate Day`, `End Phase`, `Immediate`) if the ability occurs in connection to a non-compound action
- `Compound`: Compound actions are defined in a different format see [here](#compound-actions)
- `Starting` for starting abilities
- `Passive` for constantly active abilities
- `On Death` for an ability that triggers on death
- `On Visit [[<Ability Type>]]` for triggering when any (`On Visit`) or a specific ability (e.g. `On Visit [Investigation]`) is used on the player
- `On Action [[<Ability Type>]]` for triggering when the player uses any (`On Action`) or a specific ability (e.g. `On Action [Investigation]`)

----
#### Action Restrictions

Action Restrictions can be one or more of the following in a comma separated list:
- Temporal Restriction:
  - `Temporal: <Phase>`, may only be used in `<Phase>`, e.g. `Temporal: Day 0`
  - `Temporal: <Phase>+`, may only be in `<Phase>` or after, e.g. `Temporal: Night 2+`
- Attribute Restriction:
  - `Attribute: has <Attribute>`, may only be used if `<Attribute>` is present
  - `Attribute: lacks <Attribute>`, may only be used if `<Attribute>` is not present
  - `Attribute: <Target> [has | lacks] <Attribute>`, may only be used if `<Target>` (a target type) has/lacks `<Attribute>`
- Succession Restrictions:
  - `Succession: No Succession`, may not be used in in succession
  - `Succession: No Target Succession`, may not be used successively on the same target 
- Quantity Restrictions:
  - `Quantity: <Value>`, may only used a maximum of `<Value>` times

----
#### Action Scaling

**WIP**

----
#### Action Compulsion

Action Compulsion can be `Forced` for forced actions or blank otherwise

----
#### Compound Actions

Compound actions uses an extended format. The trigger type is set as Compound, but a newline separated list of abilities is provided instead of a single ability type.

Example 1:
```
Compound:
  • <Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
  • <Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
```
Example 2:
```
<Compound>:
  • <Trigger Type>:
    ‣ <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
    ‣ <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
  • <Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
```

---
#### Complex Actions

An action can consist of several abilities chained together. The action will still have a singular feedback.

Format:
```
<Trigger Type>:
  • Process:
    ‣ <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
  • Evaluate:
    ‣ [<Condition> | Otherwise]: ['<Feedback>' | <Ability> | <Target>]
```

As a first step all the abilities from the process step are evaluated, and their feedback is stored into `@Result<n>` values. (e.g. `@Result1` for the first ability). This feedback is not given to the player.

Afterwards, each evaluate line is evaluated.
Each line consists of a `<Condition>`, or `Otherwise`. `Otherwise` triggers only if none of the other conditions trigger. Several conditions may trigger together.

Condition Format:
- `<Target> is [<Target> | '<Value>']`
- `not (<Condition>)`
- `(<Condition>) and (<Condition>)`
- `(<Condition>) or (<Condition>)`

A condition may either be a pure condition, or conditions combined with logial operations.
A pure condition can check if a `<Target>` (usually one of the `@Result<n>` values) matches another `<Target>` or a certain constant `<Value>`

If a condition triggers there are three options:
- Specify a certain `<Feedback>` that should be returned
- Execute another `<Ability>` and return that ability's feedback
- Use `<Target>` to return the feedback of one of the original abilities

----
----
### Target Types

Within abilities a selection by the player often affects the execution of the abilities. We use target types to refer to the player, their selection or similar. The following target types exist:

- `@Self`: Uses the player who this ability belongs to
- `@Selection`: Uses the player/role/etc selected in the action
- `@SecondarySelection`: Uses a secondary selection from the action (e.g. "Disguise <Selection> as <SecondarySelection>")
- `@Target`: Uses the current target set by the player
- `@(<Attribute>)`: Uses all players that have the `<Attribute>` (e.g. `@(Wolfish)`)
- `@ActionAbilityType`, `@ActionFeedback`: Provides an action's ability type and its feeback in the `On Action` trigger

----
----
### Duration Types

Many abilities apply attributes and need to specify an attribute duration. The following attribute duration types may be used:

- `~Permanent`: Lasts until role loss
- `~Persistent`: Lasts even past role lose
- `~Phase`: Lasts until the end of the current phase
- `~NextDay`, `~NextNight`: Lasts until the end of the next day/night

----
----
### Ability Type

---
#### Killing

Format: `<Subtype> <Target>`

- Subtypes: `Kill`, `Attack`, `Lynch` and `True Kill`
- Target: A target type, specifying who is getting killed

---
#### Investigating

Format:
- `<Subtype> Investigate <Target> (<Relevant Effects>)` (Investigate role/alignment/category)
- `Attribute Investigate <Target> for <Attribute>` (Look if a certain attribute is present)

- Subtypes: `Role`, `Alignment` or `Category`
- Target: A target type, specifying who is getting attacked
- Relevant Effects: A comma separated list of the following three values: `WD` (Weak Disguises), `SD` (Strong Disguises), `OB` (Obstructions), listing all of those the investigation is affected by
- Attribute: An attribute which is getting checked for

---
#### Targeting

Format: `Target <Target>`

- Target: A target type, specifying what is getting targeted

---
#### Disguising

Format: `<Subtype> Disguise <Target> as '<Role>' (<Duration>)`

- Subtype: `Weakly` or `Strongly`
- Target: A target type, specifiying who is disguised
- Role: A role, that will be the disguise
- Duration: A duration type

As a result of a Disguising the <Target> receives a Disguised attribute.

---
#### Protecting

Format:
- `Protect <Target> from '<KillingSubtype>' by <Selector> through <Subtype> during <Phase> (<Duration>)`
- `Protect <Target> from '<KillingSubtype>' by <Selector> through <Subtype> (<Duration>)`
- `Protect <Target> from '<KillingSubtype>' through <Subtype> during <Phase> (<Duration>)`
- `Protect <Target> from '<KillingSubtype>' through <Subtype> (<Duration>)` 

- Target: A target type, specifying who is protected.
- KillingSubtype: `Attacks`, `Kills` (automatically contains attacks), `Lynches` or `All`
- Selector: A target type, limits the protection to only work against certain players.
- Subtype: `Absence at <Location>`, `Active Defense`, `Passive Defense` or `Partial Defense`
  - Location: Either a channel (`#channelName`) or a target type
- Phase: `Night` or `Day`, limits the protection to only be active during a certain phase. 
- Duration: A duration type

As a result of a Protecting the <Target> receives a defense attribute.

---
#### Applying

Format:
- `Apply <Attribute> to <Target> (<AdditionalAttributeData>)` (Add a custom attribute)
- `Remove <Attribute> from <Target>` (Remove a custom or standard attribute)

- Attribute: A defined custom attribute
- Target: A target type
- Additional Attribute Data: A comma separated list of additional attribute data defined by the attribute

As a result of an Applying the <Target> receives a custom attribute.

---
#### Redirecting

Format:
- `Redirect '<Subtype>' to <Target>` (Redirect all player's abilities)
- `Redirect '<Subtype>' from <Source> to <Target>` (Redirect only specific player's abilities)

- Subtype: An ability type name, or `all` or `non-killing abilities`
- Target: A target type, specifying who to redirect to
- Source: A target type, specifying abilities from which players should be redirected

---
#### Vote Manipulating

Format: `Manipulate <Target>'s '<Subtype>' to '<Value>'`

- Target: A target type, specifying who is being manipulated
- Subtype: `public voting power`, `private voting power` or `public starting votes`
- Value: A target type, specifying the amount of votes

As a result of a Vote Manipulation the <Target> receives a vote manipulated attribute.

---
#### Whispering

Format: `Whisper to <Location> as '<Disguise>' (<Duration>)`

- Location: Either a channel (`#channelName`) or a target type, specifying to which player or channel to whisper
- Disguise: A role, or if none, remove `as <Disguise>`
- Duration: A duration type, specifying for how long the whispers lasts

As a result of a Whispering, a whispering connection is opened.

---
#### Joining

Format: `Join '<GroupName>'`

- GroupName: The name of a defined group

As a result of a Joining the <Target> receives a group membership attribute.

---
#### Granting

Format:
- `Grant '<ExtraRole>' to <Target>` (Grant an extra role)
- `Revoke '<ExtraRole>' to <Target>` (Remove an extra role)

- ExtraRole: A defined extra role
- Target: A target type, specifying who gains the extra role

As a result of a Granting the <Target> receives an Extra Role.

---
#### Loyalty

Format: `Loyalty to '<Name>' (<Subtype>)`

- Name: The name of a defined group (if subtype is group) or a defined team (if subtype is alignment)
- Subtype: `Group` or `Alignment`

Loyalty is applied only to yourself.

---
#### Obstructing

Format:
- `Obstruct <Target>` (Block all ability types)
- `Obstruct <Ability Type> for <Target>` (Block only a certain ability type)
- `Obstruct <Ability Subtype> for <Target>` (Block only a certain ability subtype)
- `Obstruct [<Ability Type>|<Ability Subtype>] for <Target> ⇒ <Feedback>` (Block a certain ability type and return fake feedback)

- Target: A target type, specifying which player should be obstructed
- Ability Type: An ability type, specifying which ability types should be obstructed
- Ability Subtype: The full name of an ability subtype, usually gained from appending the ability type name to the ability subtype name
- Feedback: A custom feedback that should be returned. The feedback is given in the normal format of the specified ability type. Possible values for the `Killing` ability type would be `Failure` or `Success`. Possible values for the `Role Investigation` ability subtype would be any role.

---
#### Poll Manipulating

Format:
- `Add <PollType> Poll` (Duplicate an existing poll)
- `Create <PollType> Poll` (Creates a poll and the role will execute the resulting ability)
- `Cancel <PollType> Poll` (Cancel the poll's resulting ability)
- `Delete <PollType> Poll` (Remove a poll that would otherwise exist)
- `Manipulate <PollType> Poll (<Target> is '<ManipulationType>')` (Manipulate a poll's candidates)

- Poll Type: A type of poll. For example `Lynch`, `Election` or otherwise defined polls.
- Target: A target type, specifying which player should be manipulated on the poll
- ManipulationType: Specifies how the player should be manipulated on the poll:
  - `Unvotable`: The player cannot be voted for on the poll
  - `Disqualified`: The player cannot win the poll, but can be voted for

---
#### Announcements

Format:
- `Reveal <Target> to <Location>` (Self Reveal, Public Information)
- ?? (Private Information)
- `Know <Information>` (Private Knowledge)

- Target: A target type, specifying what to reveal. This may be a player or a piece of information.
- Location: A channel to reveal information too
- Information: **WIP**
