# A guide on how to formalize roles/groups/teams/attributes

## Contents

- [Basics](#basics)
- [Role](#role)
- [Teams](#teams) 
- [Groups](#groups) 
- [Abilities](#abilities)
  - [Ability](#ability)
    - [Trigger Types](#trigger-types)
    - [Action Restrictions](#action-restrictions)
    - [Action Scaling](#action-scaling)
    - [Action Compulsion](#action-compulsion)
    - [Compound Actions](#compound-actions)
    - [Complex Actions](#complex-actions)
  - [Ability Set](#ability-set) 
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
    - [Role Change](#role-change)
    - [Copying](#copying)
    - [Choices](#choices)
    - [Ascend / Descend](#ascend-descend)

## Basics
- `<Argument>`: Should be replaced by something. E.g. `<Argument>` ⇒ `Example`  
- `[<Argument1> | <Argument2>]`: Should be replaced by one of the options within `[]` which are separated by `|`, e.g. `[<Argument1> | <Argument2>]` ⇒ `<Argument1>` ⇒ `Example1`  
- `[<Argument1> | Text1 | Text2]`: Should be replaced by one of the options within `[]`, e.g. `[<Argument1> | Text1 | Text2]` ⇒ `Text1`
- `[<Argument>]`: Should be replaced either by `<Argument>` or removed entirely

Due to technical limitations if you see `'`'s within `this text` consider them to be \`'s.

## Role

```
**<Role Name>** | <Role Group> <Role Category> <Role Team>
[<Role Ability List> | No Abilities]
```
- Role Name: May be anything  
- Role Group: May be `Townsfolk`, `Werewolf`, `Solo`, `Unaligned` or `Extra`  
- Role Category: May be `Elected`, `Align`, `Recruitment`, `Killing`, `Group`, `Investigative`, `Power` or `Miscellaneous`  
- Role Team: Should be kept blank for any role group besides `Solo`, should be set to `- <Team Name>`, where `<Team Name>` is the name of a defined team  

- Role Ability List: A newline separated list of `Abilities`

## Teams
```
**<Team Name>**
[<Role Ability List> | No Abilities]
```

Teams use the same general format as roles. Some trigger types are not compatible with teams.

## Groups
```
**<Group Name>** | <Related Team Name> Group
[<Role Ability List> | No Abilities]
```

Groups use the same general format as roles. Some trigger types are not compatible with groups.
When a group creates a poll, a random voting member is selected to execute the resulting ability. 

## Abilities

----
----
### Ability
Single Ability
```
<Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩
```
Multiple Abilities
```
<Trigger Type>: [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩
  • <Ability Type> 
  • <Ability Type> 
```
The three elements `[<Action Restriction>]`, `{<Action Compulsion>}` and `⟨<Action Scaling>⟩` should be left out if unnecessary.

If a single trigger does several abilities at once, they can be listed as part of one trigger as shown above.

- Trigger Types: Specifies when the ability is used. See [here](#trigger-types)
- Action Restrictions: Specifies if there are restrictions on the action. See [here](#action-restrictions)
- Action Scaling: Specifies if the amount of actions scales with player count. See [here](#action-scaling)
- Action Compulsion: Specifies if the ability is forced. See [here](#action-compulsion)
- Ability Type: Specifies which ability type and how to execute it. See [here](#ability-types)

----
#### Trigger Types

Trigger types can be one of the following:
- An Action Timing (`Start Night`, `End Night`, `Start Day`, `End Day`, `Immediate Night`, `Immediate Day`, `End Phase`, `Start Phase`, `Immediate`) if the ability occurs in connection to a non-compound action
- `Compound`: Compound actions are defined in a different format see [here](#compound-actions)
- `Starting` for starting abilities
- `Passive` for constantly active abilities
- `Passive [End Day|End Night|Start Day|Start Night]` for abilities that passively/automatically trigger at the start or end of a phase
- `On Death` for an ability that triggers on death
- `On Killed` for an ability that triggers when a player dies through a (true) kill
- `On Visit [[<Ability Type>]]` for triggering when any (`On Visit`) or a specific ability (e.g. `On Visit [Investigation]`) is used on the player (use `@Visitor` within this trigger to reference the visitor)
- `On Action [[<Ability Type>]]` for triggering when the player uses any (`On Action`) or a specific ability (e.g. `On Action [Investigation]`)
- `On Disbandment` for an ability that triggers when a group disbands
- `On Lynch` for an ability that triggers when a player is lynched (applies even if the lynch is avoided) (Use `@Attacker` within this trigger to reference the player (if existing) responsible for the lynch)
- `On [Passive|Partial] Defense` for an ability that triggers when a passive or partial defense is used (Use `@Attacker` within this trigger to reference the player (if existing) responsible for the defense being used.)

----
#### Action Restrictions

Marked by square brackets: []

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

Marked by angle brackets: ⟨⟩

May be one of the following:
- Static Scaling: The action can always be used several times
  - `x<Value>`, specify a number (`Value`) that determines how many times the action can be used
- Dynamic Scaling: Amount of action usages depend on total player count
  - `[Total|Living] Players/<Value>` the action can be used exactly as many times as the player count divided by the specified `Value`, rounded down
  - `[Total|Living] Players: <Comparison><Value> ⇒ <Count>` a comma separated list of conditions, where `Comparison` may be `<`, `>`, `≤`, `≥` or `=` and `Value` is a constant number specifying a player amount and `Count` is the amount of available uses. The default is always 1, so in situations with two cases, where the count for one case is 1, it suffices to specify the other case.

----
#### Action Compulsion

Marked by curly brackets: {}

Action Compulsion can be `Forced` for forced actions or blank otherwise

----
#### Compound Actions

Compound actions uses an extended format. The trigger type is set as Compound, but a newline separated list of abilities is provided instead of a single ability type.

Example 1:
```
Compound:
  • <Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩
  • <Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩
```
Example 2:
```
<Compound>:
  • <Trigger Type>: [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩
    ‣ <Ability Type>
    ‣ <Ability Type>
  • <Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩
```

---
#### Complex Actions

An action can consist of several abilities chained together. The action will still have a singular feedback.

Format:
```
<Trigger Type>:
  • Action: [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩
  • Process:
    ‣ <Ability Type>
    ‣ <Ability Type>
  • Evaluate:
    ‣ [<Condition> | Otherwise]: ['<Feedback>' | <Ability> | <Target>]
    ‣ [<Condition> | Otherwise]: ['<Feedback>' | <Ability> | <Target>]
```
Compact Format:
```
<Trigger Type>:
  • Action: [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩
  • Process: <Ability Type>
  • Evaluate: [<Condition> | Otherwise]: ['<Feedback>' | <Ability> | <Target>]
```

If no action restriction/compulsion/scaling applies, the `Action` line should be left out.

As a first step all the abilities from the process step are evaluated, and their feedback is stored into `@Result<n>` values. (e.g. `@Result1` for the first ability). This feedback is not given to the player. In case of a single process step only, the result can be accessed through `@Result`.

Afterwards, each evaluate line is evaluated.
Each line consists of a `<Condition>`, or `Otherwise`. `Otherwise` triggers only if none of the other conditions trigger. Several conditions may trigger together.

If only a single condition line with feedback "Success" is provided, an `Otherwise: Failure` line is implied.

Condition Format:
- `<Target> is [<Target> | '<Value>']` (Pure)
- `<Target> is not [<Target> | '<Value>']` (Pure)
- `not (<Condition>)`
- `(<Condition>) and (<Condition>)`
- `(<Condition>) or (<Condition>)`

A condition may either be a pure condition, or conditions combined with logial operations.
A pure condition can check if a `<Target>` (usually one of the `@Result<n>` values) matches another `<Target>` or a certain constant `<Value>` (or if it does not match)

If a condition triggers there are three options:
- Specify a certain `<Feedback>` that should be returned
- Execute another `<Ability>` and return that ability's feedback
- Use `<Target>` to return the feedback of one of the original abilities

---
---
### Ability Set

A role can inherit abilities shared by several roles through an ability set.

To inherit all abilities from an ability set use the trigger type `Inherits: '<AbilitySetName>'`

An ability set has the same format as a role, except for the first line which is `**<AbilitySetName>** | Ability Set`

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
- `@Members`: Uses all players that are part of the current group or team

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
`<Subtype> Investigate <Target> (<Disguise Level>)` (Investigate role/alignment/category) - Returns the Role/Alignment/Category  
`Attribute Investigate <Target> for '<Attribute>'` (Look if a certain attribute is present) - Returns Success/Failure  
`Investigate <Target> Role Count (<Disguise Level>)` (Investigates the amount of times a certain role exists)  

- Subtypes: `Role`, `Alignment` or `Category`
- Target: A target type, specifying who is getting attacked
- Relevant Effects: A comma separated list of the following two values: `WD` (Weak Disguises) or `SD` (Strong Disguises), listing all of those the investigation is affected by. Leave this out if not affected by any disguises.
- Attribute: An attribute which is getting checked for

---
#### Targeting

Format: `Target <Target> [<Type>]`

- Target: A target type, specifying what is getting targeted
- Type: Specifies what type of object can be targeted. May be `Player` (a player besides the player themselves), `Role`, `Attribute` or `List: <ListElements>` (with several `Elements` that are comma separated)

---
#### Disguising

Format: `<Subtype> Disguise <Target> as '<Role>' (<Duration>)`

- Subtype: `Weakly` or `Strongly`
- Target: A target type, specifiying who is disguised
- Role: A role, that will be the disguise
- Duration: A duration type. Defaults to `~Permanent`, leave out if unnecessary.

As a result of a Disguising the <Target> receives a `Disguise` attribute.

---
#### Protecting

Format:  
`Protect <Target> from '<KillingSubtype>' by <Selector> through <Subtype> during <Phase> (<Duration>)`  
`Protect <Target> from '<KillingSubtype>' by <Selector> through <Subtype> (<Duration>)`  
`Protect <Target> from '<KillingSubtype>' through <Subtype> during <Phase> (<Duration>)`  
`Protect <Target> from '<KillingSubtype>' through <Subtype> (<Duration>)`   

- Target: A target type, specifying who is protected.
- KillingSubtype: `Attacks`, `Kills` (automatically contains attacks), `Lynches` or `All`
- Selector: A target type, limits the protection to only work against certain players.
- Subtype: `Absence at <Location>`, `Active Defense`, `Passive Defense` or `Partial Defense`
  - Location: Either a channel (`#channelName`) or a target type
- Phase: `Night` or `Day`, limits the protection to only be active during a certain phase. 
- Duration: A duration type. Defaults to `~Permanent`, leave out if unnecessary.

As a result of a Protecting the <Target> receives a `Defense` attribute.

---
#### Applying

Format:  
`Apply '<Attribute>' to <Target> (<Duration>) (<AdditionalAttributeData>)` (Add a custom attribute)  
`Remove '<Attribute>' from <Target>` (Remove a custom or standard attribute)  
`Remove '<Attribute>' applied by <Target> from <Target>` (Remove a custom or standard attribute applied by a certain player)  

- Attribute: (apply) A defined custom attribute, (remove) a defined custom or a standard attribute 
- Target: A target type 
- Duration: A duration type. Defaults to `~Permanent`, leave out if unnecessary.
- Additional Attribute Data: A comma separated list of additional attribute data defined by the attribute. Leave out if unnecessary.

As a result of an Applying the <Target> receives a custom attribute.

---
#### Redirecting

Format:  
`Redirect '<Subtype>' to <Target>` (Redirect all player's abilities)  
`Redirect '<Subtype>' from <Source> to <Target>` (Redirect only specific player's abilities)  

- Subtype: An ability type name, or `all` or `non-killing abilities`
- Target: A target type, specifying who to redirect to
- Source: A target type, specifying abilities from which players should be redirected

May be used in combination with a `Passive` trigger.

---
#### Vote Manipulating

Format: `Manipulate <Target>'s '<Subtype>' to '<Value>' (<Duration>)`

- Target: A target type, specifying who is being manipulated
- Subtype: `public voting power`, `private voting power` or `public starting votes` (also `lynch starting votes` and `election starting votes`)
- Value: A target type, specifying the amount of votes
- Duration: A duration type, specifying for how long the vote manipulation lasts. Defaults to `~Permanent`, leave out if unnecessary.

As a result of a Vote Manipulation the <Target> receives a `Manipulation` attribute.

---
#### Whispering

Format: `Whisper to <Location> as '<Disguise>' (<Duration>)`

- Location: Either a channel (`#channelName`) or a target type, specifying to which player or channel to whisper
- Disguise: A role, or if none, remove `as <Disguise>`
- Duration: A duration type, specifying for how long the whispers lasts. Defaults to `~Permanent`, leave out if unnecessary.

As a result of a Whispering, a whispering connection is opened.

---
#### Joining

Format: `Join '<GroupName>'`

- GroupName: The name of a defined group

As a result of a Joining the <Target> receives a `Group Membership` attribute.

---
#### Granting

Format:  
`Grant '<ExtraRole>' to <Target>` (Grant an extra role)  
`Revoke '<ExtraRole>' from <Target>` (Remove an extra role)  

- ExtraRole: A defined extra role
- Target: A target type, specifying who gains the extra role

As a result of a Granting the <Target> receives an Extra Role.

---
#### Loyalty

Format: `Loyalty to '<Name>' (<Subtype>)`

- Name: The name of a defined group (if subtype is group) or a defined team (if subtype is alignment)
- Subtype: `Group` or `Alignment`

Loyalty is applied only to yourself.

May be used in combination with a `Passive` trigger.

---
#### Obstructing

Format:  
`Obstruct <Target> (<Duration>)` (Block all ability types)  
`Obstruct <Ability Type> for <Target> (<Duration>)` (Block only a certain ability type)  
`Obstruct <Ability Subtype> for <Target> (<Duration>)` (Block only a certain ability subtype)  
`Obstruct [<Ability Type>|<Ability Subtype>] for <Target> ⇒ <Feedback> (<Duration>)` (Block a certain ability type and return fake feedback)  

- Target: A target type, specifying which player should be obstructed
- Ability Type: An ability type, specifying which ability types should be obstructed
- Ability Subtype: The full name of an ability subtype, usually gained from appending the ability type name to the ability subtype name
- Feedback: A custom feedback that should be returned. The feedback is given in the normal format of the specified ability type. Possible values for the `Killing` ability type would be `Failure` or `Success`. Possible values for the `Role Investigation` ability subtype would be any role.
- Duration: A duration type. Defaults to `~Permanent`, leave out if unnecessary.

As a result of an Obstruction the <Target> receives an `Obstructed` attribute.

---
#### Poll Manipulating

Format:  
`Add <PollType> Poll` (Duplicate an existing poll)  
`Create <PollType> Poll` (Creates a poll and the role will execute the resulting ability)  
`Cancel <PollType> Poll` (Cancel the poll's resulting ability)  
`Delete <PollType> Poll` (Remove a poll that would otherwise exist)  
`Manipulate <PollType> Poll (<Target> is '<ManipulationType>')` (Manipulate a poll's candidates)  

- Poll Type: A type of poll. For example `Lynch`, `Election` or otherwise defined polls.
- Target: A target type, specifying which player should be manipulated on the poll
- ManipulationType: Specifies how the player should be manipulated on the poll:
  - `Unvotable`: The player cannot be voted for on the poll
  - `Disqualified`: The player cannot win the poll, but can be voted for

---
#### Announcements

Format:  
`Reveal [<Target>|<Information>] to <Location>` (Self Reveal, Public Information)  
`Learn '<Information>'` (Private Information)  
`Know '<Information>'` (Private Knowledge)  

- Target: A target type, specifying what to reveal. This may be a player or a piece of information.
- Location: A channel to reveal information too
- Information: (reveal) Text that is revealed, (learn, know) Text the player receives

---
#### Role Change

Format: `Role Change <Target> to <Role>`

- Target: A target type, specifying which player's role to change
- Role: A target type or constant role, specifying the player's new role

---
#### Copying

Format:  
`Copy <Target>` (Copies all abilities of target role or player to yourself)  
`Duplicate <Target>'s abilities` (Copies all abilities of target role or player to themself)  
`Copy <Target> to <Target2>` (Copies all abilities of a target role or player to another player)  

- Target: A target type, specifying a role or player to copy from
- Target2: Another target type, specifying who should get the copied ability

---
#### Choices

Choice Creation Format:
```
'<Name>' Choice Creation [for <Target|Location>]:
  • <Option Name>:
    ‣ <Resulting Ability>
    ‣ <Resulting Ability>
  • <Option Name>:
    ‣ <Resulting Ability>
```

- Name: Gives the choice a name to reference later
- Target|Location: Optional, specifies to which player, target type or location the choice should be sent. If not specified the current player chooses.
- Option Name: The names for each option which are what can be selected by choice choosing
- Resulting Ability: Abilities that result after a choice choosing, executed by the role who created the choice

Choice Choosing Format:
```
'<Name>' Choice Choose '<Option Name>'
```

- Name: The choice to choose one
- Option Name: The name of the chosen option

---
#### Ascend Descend

Format:
- `Ascend`
- `Descend`

