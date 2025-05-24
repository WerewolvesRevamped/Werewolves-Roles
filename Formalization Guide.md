# A guide on how to formalize all the WWR game elements

## Contents

- [Basics](#basics)
- [Game Elements](#game-elements)
- [Variables](#variables)
- [Role](#role)
- [Teams](#teams) 
- [Groups](#groups)
- [Polls](#polls)
- [Locations](#locations)
- [Attributes](#attributes)
  - [Standard Attributes](#standard-attributes)
    - [Disguise Attributes](#disguise-attributes)
    - [Defense Attributes](#defense-attributes)
    - [Absence Attributes](#absence-attributes)
    - [Manipulation Attributes](#manipulation-attributes)
    - [Group Membership Attributes](#group-membership-attributes)
    - [Obstruction Attributes](#obstruction-attributes)
    - [Poll Attributes](#poll-attributes)
    - [Role Attributes](#role-attributes)
    - [Whispering Attributes](#whispering-attributes)
    - [Loyalty Attributes](#loyalty-attributes)
    - [Redirection Attributes](#redirection-attributes)
  - [Custom Attributes](#custom-attributes)
- [Counter](#counter)
- [Abilities](#abilities)
  - [Ability](#ability)
    - [Trigger Types](#trigger-types)
    - [Action Restrictions](#action-restrictions)
    - [Action Scaling](#action-scaling)
    - [Action Compulsion](#action-compulsion)
    - [Complex Actions](#complex-actions)
  - [Ability Set](#ability-set) 
  - [Requirements](#requirements) 
  - [Target Types](#target-types)
    - [Property Access](#property-access)
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
    - [Changing](#changing)
    - [Copying](#copying)
    - [Choices](#choices)
    - [Ascend / Descend](#ascend-descend)
    - [Disband](#disband)
    - [Counting](#counting)
    - [Conversation Reset](#conversation-reset)
    - [Cancel](#cancel)
    - [Switching](#switching)
    - [Shuffle](#shuffle)
    - [Emitting](#emitting)
    - [Displaying](#displaying)
- [Host Information](#host-information)

## Basics
- `<Argument>`: Should be replaced by something. E.g. `<Argument>` ⇒ `Example`  
- `[<Argument1> | <Argument2>]`: Should be replaced by one of the options within `[]` which are separated by `|`, e.g. `[<Argument1> | <Argument2>]` ⇒ `<Argument1>` ⇒ `Example1`  
- `[<Argument1> | Text1 | Text2]`: Should be replaced by one of the options within `[]`, e.g. `[<Argument1> | Text1 | Text2]` ⇒ `Text1`
- `[<Argument>]`: Should be replaced either by `<Argument>` or removed entirely

Due to technical limitations if you see `'`'s within `this text` consider them to be \`'s.

In cases where lists within lists within lists are used, use the following format:

```
• Level 1
  ‣ Level 2
    ◦ Level 3
      · Level 4
        ⁃ Level 5
          ⹀ Level 6
```

## Game Elements

The game consists out of passive and active game elements. Active elements can be modified, while passive elements remain static.

The following game elements exist:
- Players (Active): Players consist out of roles (primary role and extra roles), their identity (person and ingame identity), player attributes, their living status (dead or alive), their alignment (always the same as the role's team for non-unaligned players), a target and a counter.
- Roles (Passive): Roles consist out of a team, a category, a class, abilities and role attributes
- Teams (Active/Passive): Teams consist out of a class, a counter, attributes, team wide abilities
- Groups (Active/Passive): Groups consist out of a team, a target, group wide abilities and a counter
- Polls (Active): Polls consist out of a type, votable options, allowed voters, a target, and poll abilities
- Attributes (Active/Passive): Attributes consist out of several values such as duration, owner role, owner name and custom values
- Abilities (Passive): Abilities consist out of an ability type and various parameters

## Variables

Some game values can be accessed from variables:
- `$total`: Retrieves the total player count
- `$living`: Retrieves the amount of living players
- `$phase`: Gets the current phase name

## Role

```
**<Role Name>** | <Role Group> <Role Category> <Role Team>
[Unique Role]
[Haunted Role]
[<Role Ability List> | No Abilities]
```
- Role Name: May be anything  
- Role Group: May be `Townsfolk`, `Werewolf`, `Solo`, `Unaligned` or `Extra`  
- Role Category: May be `Elected`, `Align`, `Recruitment`, `Killing`, `Group`, `Investigative`, `Power` or `Miscellaneous`  
- Role Team: Should be kept blank for any role group besides `Solo`, should be set to `- <Team Name>`, where `<Team Name>` is the name of a defined team
- Unique Role: Set to `Unique Role` for unique roles, otherwise remove the line entirely
- Haunted Role: Set `Haunted Role` for roles that use ALL their ability while ghostly, otherwise remove the line entirely

- Role Ability List: A newline separated list of `Abilities`

## Teams
```
**<Team Name>**
Win Condition: <Target List>
<Role Ability List>
```

Win Condition: A comma separated list of target types. The team wins when all remaining players match one or more of the targets.

Teams use the same general format as roles. Some trigger types are not compatible with teams.

When refering to a team you may use `&<TeamName>`.

## Groups
```
**<Group Name>** | <Related Team Name> Group
[Unique Group]
[<Group Ability List> | No Abilities]
```

Groups use the same general format as roles. Some trigger types are not compatible with groups.
When a group creates a poll, a random voting member is selected to execute the resulting ability. 
- Unique Group: Set to `Unique Group` for unique group, otherwise remove the line entirely 

When refering to a group you may use `#<GroupName>`.

When using several instance of the same group they can be refered to as `#<Group Name>:<Identifier>`, identifier may be a role/player/text/etc and is not visible to the members.

## Public Channels

The following public channels exist and can be referenced like groups:
- #story_time
- #town_square
- #tavern
- #voting_booth

## Polls
```
**<Poll Name>** | Poll
Available Options: <Options>
Allowed Voters: <Voters>
[Show Voters: [Yes|No]]
[Random: <Random Selector>]
[<Poll Opening Trigger>: Create Poll in <Location> [as <Name>]]
[On Poll Closed: <Poll Close Ability>]
```

- Options: A comma separated list of votable options. May contain one or more selectors.
- Voters: A comma separated list of allowed voters. May contain one or more selectors.
- Show Voters: Specifies whether the result shows who voted what (yes) or just the final vote count per option (no). Defaults to `yes`.
- Random: A selector that is used to select a random player from when "Random" is specified as an available option. Defaults to `@All`.
- Poll Opening Trigger: The poll may open on a specified trigger type. Additional abilities may be executed as part of this trigger.
- Poll Closed Ability: An ability to execute on poll closing

  
## Locations
```
**<Location Name>**
Sort Index: <Number>
Members: <Player Type List>
Viewers: <Player Type List>
```

- Sort Index: A number indicating at what position to sort the location
- Members: A command separated list of player types - players that can both view and write in the channel
- Viewers: A comma separated list of player types - players that can view but not write in the channel

Valid player types are: Alive, Dead, Ghost, Substitute, Spectator
Instead of a player type list, `*None*` or `*All*` may be used.

## Attributes

All attributes have source role and source player value. Attribute abilities are executed by the attribute source player.

All attributes can be refered to by `<AttributeType>:<SourceRole>` or `<AttributeType>:<SourcePlayer>`.
Examples:
- `Disguise:Tanner`, a disguise applied by a tanner
- `Defense:Target`, a defense applied by the player's current target. The @ symbol is skipped.

Additionally attributes can be refered to by `<AttributeType>::<Value1>`

---
---
### Standard Attributes

Each type of standard attribute has additional values.
There are a few types of standard attributes:

---
#### Disguise Attributes

Disguise attributes have two additional values:
- Disguise Role: The role of the disguise
- Disguise Strength: Whether the disguise is weak or strong

---
#### Defense Attributes

Defense attributes have four additional values:
- Defense Subtype: Which type of defense is used (passive/partial/active/absence/recruitment)
- Killing Subtype: Which type of killing is defended against
- Affected Players: A group of players for which the defense works (defaults to all players)
- Affected Phase: Specifies if the defense is only active during day or night (defaults to both)

A defense membership attribute can additionally be refered to by `Defense:<DefenseSubtype>`, e.g. `Defense:Active`

---
#### Absence Attributes

Absence attributes have four additional values:
- Killing Subtype: Which type of killing is defended against
- Affected Players: A group of players for which the defense works (defaults to all players)
- Affected Phase: Specifies if the defense is only active during day or night (defaults to both)
- Absence Location: Where the player is absent at

---
#### Manipulation Attributes

Manipulation attributes have two additional values:
- Manipulation Subtype: Which type of vote manipulation is applied
- Manipulation Value: The value of the vote manipulation

---
#### Group Membership Attributes

Group Membership attributes have two additional values:
- Group Name: The name of the relevant group
- Membership Type: The type of group membership (defaults to Member)

A group membership attribute can additionally be refered to by `GroupMembership:<GroupName>`, e.g. `GroupMembership:Baker`.

---
#### Obstruction Attributes

Obstruction attributes have two additional values:
- Affected Abilities: A list of ability types or subtypes that are affected by the obstruction
- Obstruction Feedback: The fake feedback which is returned (if applicable) (defaults to none)

---
#### Poll Attributes

Poll attributes have two or three additional values:
- Poll Manipulation Subtype: The poll manipulation subtype
- Poll Count: For count subtype, a value modifying the amount of polls
- Target: For result subtype, a player that is having their result manipulated on the poll
- Poll Result Manipulation Type: For result subtype, the type of manipulation done to the target

---
#### Role Attributes

Role attributes have one additional value:
- Role: The role that is being added through the role attribute

---
#### Whispering Attributes

Whispering attributes have three additional value. These are highly technical and are not useful for manipulation or access:
- Connection Name: The name of the connection they belong to
- Source Channel: Originating channel
- Target Channel: Target channel
  
---
#### Loyalty Attributes

Loyalty attributes have two additional value. 
- Loyalty Subtype: Either "group" or "alignment" loyalty
- Loyalty Target: The group or alignment the loyalty is towards
  
---
#### Redirection Attributes

Redirection attributes have three additional value. 
- Redirection Target: The player that will be redirected to
- Source Filter: A selector that is evaluated at *redirect* time and must contain the player who used the ability
- Ability Type Filter: An ability type/subtype/category which must match/contain the ability that is to be redirected 

----
----
### Custom Attributes
```
**<AttributeName>** | Attribute
[<Role Ability List> | No Abilities]
```

Attributes use the same general format as roles. Some trigger types are not compatible with attributes.

To reference a custom attribute simply using `'<AttributeName>'` suffices.

To select a custom attribute with a specific custom value you can use `'<AttributeName>:<Value>'`

---
## Counter

Some game elements (groups and roles) have a counter. The value of the counter can be manipulated through [counting](#counting) and can be checked through [complex actions](#complex-actions).

## Abilities

----
----
### Ability
Single Ability
```
<Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩ |<Prompt Overwrite>|
```
Multiple Abilities
```
<Trigger Type>: [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩ |<Prompt Overwrite>|
  • <Ability Type> 
  • <Ability Type> 
```
The four elements `[<Action Restriction>]`, `{<Action Compulsion>}`, `⟨<Action Scaling>⟩` and `|<Prompt Overwrite>|` should be left out if unnecessary.

If a single trigger does several abilities at once, they can be listed as part of one trigger as shown above.

- Trigger Types: Specifies when the ability is used. See [here](#trigger-types)
- Action Restrictions: Specifies if there are restrictions on the action. See [here](#action-restrictions)
- Action Scaling: Specifies if the amount of actions scales with player count. See [here](#action-scaling)
- Action Compulsion: Specifies if the ability is forced. See [here](#action-compulsion)
- Prompt Overwrite: Specifies the value of a prompt that should be used to overwrite the default ability prompt. You may also specify `silent:<prompt overwrite>` to use a specific prompt and to remove the default prompt pinging.
- Ability Type: Specifies which ability type and how to execute it. See [here](#ability-types). If no ability is used, use `*Nothing*`

----
#### Trigger Types

Trigger types can be one of the following:
- An Action Timing (`Start Night`, `End Night`, `Start Day`, `End Day`, `Immediate Night`, `Immediate Day`, `End Phase`, `Start Phase`, `Immediate`, `Pre-End Night`, `Pre-End Day`) if the ability occurs in connection to an action. These are *always connected to an action*. 
- `Starting` for starting abilities
- `Passive` triggers whenever a significant change occurs (game start, phase change, somebody dies/changes roles) and can be used with conditions that should theoretically be checked constantly 
- `Passive [End Day|End Night|Start Day|Start Night|Start Phase|End Phase]` for abilities that passively/automatically trigger at the start or end of a phase. These occur at the same time as action timings from above, but *automatically*.
- `On Death` for an ability that triggers on death (Use `@Attacker` within this trigger to reference the player (if existing) responsible for the killing, use `@DeathType` to reference the type of killing that killed the player, use `@AttackSource` to get the source of the attack)
- `On <Target> Death` for an ability that triggers on the death of a certain target type (when several players are contained in the target, it triggers when *any* player from that target type dies) (Use `@Attacker` within this trigger to reference the player (if existing) responsible for the killing, use `@DeathType` to reference the type of killing that killed the player, use `@This` to reference the player that died, use `@AttackSource` to get the source of the attack)
- `On Killed` for an ability that triggers when a player dies through a (true) kill or attack (Use `@Attacker` within this trigger to reference the player (if existing) responsible for the killing, use `@AttackSource` to get the source of the attack). Differs from `On Death` in that it does not trigger on a lynch.
- `On Banishment` equivalent to `On Killed` but for (true) banishments
- `On Banished` equivalent to `On Death` for for banishments
- `On <Target> Banished`, see `On <Target> Death` and `On Banished`
- `On Visited [[<Ability Type>]]` for triggering when any (`On Visited`) or a specific ability (e.g. `On Visited [Investigation]`) is used on the player (use `@Visitor` within this trigger to reference the visitor, use `@VisitParameter` to access the secondary selection of the visit, use `@VisitType` to get the ability type causing the visit). Triggers before any of the visits resulting abilities.
- `On <Target> Visited [[<Ability Type>]]` for triggering when a certain target type is visited with a specific ability (e.g. `On <> Visited [Investigation]`) (use `@Visitor` within this trigger to reference the visitor, `@This` to reference the visited player, use `@VisitParameter` to access the secondary selection of the visit, use `@VisitType` to get the ability type causing the visit).  Triggers before any of the visits resulting abilities.
- `On Action [[<Ability Type>]]` for triggering when the player uses any (`On Action`) or a specific ability (e.g. `On Action [Investigation]`) (Use `@ActionTarget` to select the player the action is being used on, `@ActionResult` for the action's result, `@ActionFeedback` to get the action feedback)
- `On Any Action` for trigger for absolutely any action taken by a player. Normal `On Action` requires the action to have been taken by the same role/source.
- `On Any Action [[<Ability Type>]]`, see On Action/On Any Action
- `On <Target> Action [[<Ability Type>]]` for triggering when a certain target type uses a action with a specific ability (e.g. `On <> Action [Investigation]`) (Use `@ActionTarget` to select the player the action is being used on, `@ActionResult` for the action's result, `@This` to reference the visited player).
- `On Disbandment` for an ability that triggers when a group disbands
- `On Lynch` for an ability that triggers when a player is lynched (applies even if the lynch is avoided) (Use `@Attacker` within this trigger to reference the player (if existing) responsible for the lynch, use `@AttackSource` to get the source of the attack.
- `On [Active|Passive|Partial|Recruitment|Absence] Defense` for an ability that triggers when an active, passive, partial, recruitment or absence defense is used (Use `@Attacker` within this trigger to reference the player (if existing) responsible for the defense being used, use `@AttackSource` to get the source of the attack, use `@KillingType` to get the type of killing that the defense blocked)
- `On Redirect` for an ability that triggers when a redirect is used. (use `@Visitor` within this trigger to reference the visitor that was redirected)
- `On Betrayal` this trigger type can be used in groups to trigger when a player loyal to the group betrays it
- `On Poll Closed` triggers when a poll created by the current player/group/poll through poll creation is closed. `@Winner` can be used to reference the winner of the poll. Use `@Voters` to select all people that voted for the lynched player, `@OtherVoters` same as `@Voters` but excluding `@Self`)
- `On Poll Win` triggers when the current player wins any poll. Use `@Voters` to select all people that voted for the lynched player, `@OtherVoters` same as `@Voters` but excluding `@Self`)
- `On Poll '<Name>' Win` triggers when the current player wins a poll with name `<Name>`. Use `@Voters` to select all people that voted for the lynched player, `@OtherVoters` same as `@Voters` but excluding `@Self`)
- `On Role Change` triggers on a role change (use `@RoleChanger` to get the responsible player)
- `On Removal` triggers when the current attribute is removed. Can only be used inside attributes.
- `On End` triggers when the game ends
- `On '<Value>' Emitted` triggers when an emitting action is used to emit a specific value.
- `On Join` triggers for a team when it a new player joins it. (Use `@Joiner` to get the joining player)

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
  - `Quantity: <Value>`, may only used a maximum of `<Value>` times.
- Condition Restrictions:
  - `Condition: <Condition>`, provide a condition in the same format as in [complex actions](#complex-actions).
- (Living) Status Restriction:
  - `Status: Ghostly`, may only be used while ghostly
  - `Status: Any`, may be used while alive or ghostly
  - `Status: Alive`, may be used while alive only (default!)
 

----
#### Action Scaling

Marked by angle brackets: ⟨⟩

May be one of the following:
- Static Scaling: The action can always be used several times
  - `x<Value>`, specify a number (`Value`) that determines how many times the action can be used
- Dynamic Scaling: Amount of action usages depend on total player count
  - `[$total|$living]/<Value>` the action can be used exactly as many times as the player count divided by the specified `Value`, rounded down
  - `[$total|$living]<Comparison><Value> ⇒ <Count>` a comma separated list of conditions, where `Comparison` may be `<`, `>`, `≤`, `≥` or `=` and `Value` is a constant number specifying a player amount and `Count` is the amount of available uses. The default value for scaling is 1, but each failed condition sets the value to 0, so a condition should be specified first, followed by a default value.
- Phase Specific Scaling: The amount of action usages depends on the phase:
  - `Odd: <Value>, Even: <Value>` to specify different multiplicities of ability use in even and odd phases. Set `<Value>` to one of the other scaling types, e.g. `Odd: x1, Even: x2`

----
#### Action Compulsion

Marked by curly brackets: {}

Action Compulsion can be `Forced` for forced actions or blank otherwise. By default a forced action randomly selects a target where necessary. If a forced action should select a specific target use `Forced: <Default Target>` instead.

An action may also be marked as `{Direct}` to make it unaffected by redirections.

An action may also be marked as `{Repeating}` in which case it can be repeated, assuming the restrictions allow it.

An action may also be marked as `{Visitless}` in which case it does not count as a visit and can additionally not be redirected.

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
No Process Format:
```
<Trigger Type>: [<Action Restriction>] {<Action Compulsion>} ⟨<Action Scaling>⟩
  • [<Condition> | Otherwise]: ['<Feedback>' | <Ability> | <Target>]
```

If no action restriction/compulsion/scaling applies, the `Action` line should be left out.

In some cases Process can be left out. Then evaluate doesn't have to be specified and can be used to evaluate preexisting info.

As a first step all the abilities from the process step are evaluated, and their feedback is stored into `@Result<n>` values. (e.g. `@Result1` for the first ability). This feedback is not given to the player. In case of a single process step only, the result can be accessed through `@Result`.

Afterwards, each evaluate line is evaluated.
Each line consists of a `<Condition>`, or `Otherwise`. `Otherwise` triggers only if none of the other conditions trigger. Only a single conditions may trigger, further conditions are not evaluated.

When using `Continue` as the last ability of a condition with several abilities attached, the evaluation of conditions will __continue__ instead of being halted as usual.

In some cases it is useful to use evaluate without any conditions. In this case all evaluate lines without a condition should be put before any conditions, and they will always be executed. If no condition is specified at all, the default behaivor is to return no feedback at all as usually the feedback of the succeeding condition is returned. This can be worked around by specifying a `Feedback: <Ability>` line (this is equal in effect to specifying a single `Otherwise` line, but sounds more appropriate).

If only a single condition line with feedback "Success" is provided, an `Otherwise: Failure` line is implied.

Condition Format:
- `<Target> is [<Target> | '<Value>']` (Pure)
- `<Target> [>|<] '<Value>'` (Pure)
- `<Target> is not [<Target> | '<Value>']` (Pure)
- `<Target> exists` (Pure)
- `<Target> has <Attribute>` (Pure)
- `<Target> is in #<GroupName>` (Pure)
- `<Target> is part of <Target>` (Pure)
- `not (<Condition>)`
- `(<Condition>) and (<Condition>)`
- `(<Condition>) or (<Condition>)`

A condition may either be a pure condition, or conditions combined with logial operations.
A pure condition can check if a `<Target>` (usually one of the `@Result<n>` values) matches another `<Target>` or a certain constant `<Value>` (or if it does not match), or if a certain `<Target>` exists (e.g. to check for a living target), or if a `<Target>` has a certain `<Attribute>`, or if a numeric value or the counter is greater/lesser than another value, or if a target is part of a group, or if a target is inside another target

If a condition triggers there are three options:
- Specify a certain `<Feedback>` that should be returned
- Execute another `<Ability>` and return that ability's feedback
- Use `<Target>` to return the feedback of one of the original abilities



Another form of complex actions are for each actions, which run abilities for each player inside a target.

For Each Format:
```
For Each <Target>:
  • <Ability>
```

Inside For Each you may use `@Ind` to reference the individual player.

---
---
### Ability Set

A role can inherit abilities shared by several roles through an ability set.

To inherit all abilities from an ability set use the trigger type `Inherit: '<AbilitySetName>'`

An ability set has the same format as a role, except for the first line which is `**<AbilitySetName>** | Ability Set`

---
---
### Requirements

A role can require another role to exist.

To require a role use the trigger type `Require: '<RoleName>'`

---
---
### Role Attributes

A role can get role attributes.

To receive a single role attribute use `Role Attribute: '<Attribute Name>'`

Role attributes are defined the same way as other attributes, but operate differently from other attributes in that they are part of the role, not the player and can thus be hidden by disguises.

---
---
### Identity

A role can have a different identity

For identity use `Identity: '<Role Name>'`

When a role has a different identity its initial DM, info message and channel all appear as that role, though they will still get prompted and seen as their actual role.

---
---
### Include

A role can include another role's trigger, dynamically

To include another role use `Include: <Role>`

This way a role can be included, you can use static role references or selectors. Only triggers of the other role are included.

----
----
### Target Types

Within abilities a selection by the player often affects the execution of the abilities. We use target types to refer to the player, their selection or similar. The following target types exist:

__General Target Types__  
- `@Self`: Uses the player who this ability belongs to, or when using it inside a group, the current group, or when inside an attribute the player who has the attribute
- `@Selection`: Uses the player/role/etc selected in the action
- `@Selection[<Type>]`: Specifies a selection must be from a specific type. Can be `Player`, `Dead`, `Role`, `Category`, `Full Category`, `Attribute` or `Boolean`.
- `@SecondarySelection`: Uses a secondary selection from the action (e.g. "Disguise <Selection> as <SecondarySelection>")
- `@SecondarySelection[<Type>]`: See `@Selection:<Type>`
- `@Target`: Uses the current target set by the player
- `@TargetDead`: Uses the current target set by the player, even if it is dead.
- `@All`: Uses all living players
- `@Others`: Uses all living players except for `@Self`
- `@Dead`: Uses all dead players
- `@DeadAlive`: Uses **all** players, both dead and alive

__Advanced Target Types__  
Select living players by a certain attribute of them or their role.
- `@(Attr:<Attribute>)`: Uses all players that have the `<Attribute>` custom attribute (e.g. `@(Attr:Wolfish)`)
- `@(AttrSelf:<Attribute>)`: Uses all players that have the `<Attribute>` custom attribute applied by the current element (e.g. `@(AttrSelf:Warded)`)
- `@(AttrRole:<Role>)`: Uses all players that have the role attribute with a certain role `<Role>` (e.g. `@(AttrRole:Mayor)`)
- `@(AttrDisguise:<Target>)`: Uses all players that have the `<Target>` role applied by a certain player. Specify a target without @. (e.g. `@(AttrDisguise:Self)`)
- `@(Group:<Group>)`: Uses all players that are part of the `<Group>` group
- `@(Align:<Alignment>)`: Uses all players that are of the `<Alignment>` alignment
- `@(Role:<Role>)`: Uses all players that have the `<Role>` role
- `@(Cat:<Category>)`: Uses all players who's role is in the `<Category>` category
- `@(Class:<Class>)`: Uses all player who are part of the specified role class (Townsfolk, Werewolf, Solo, Unaligned, Extra)
- `@(FullCat:<FullCategory>)`: Uses all players who are part of a specified full category (Class + Category)
- `@(OrigFullCat:<FullCategory>)`: Uses all players who were part of a specified full category (Class + Category) at the start of the game
- `@(OrigRole:<Role>)`: Uses all players with a certain original role
- `@(OrigAlign:<Alignment>)`: Uses all players with a certain original alignment
- `@(AliveOnly:True)` / `@(AliveOnly:False)`: set to true by default, add this parameter as set to false to be able to access dead players too.
- `@(SelectAll:True)` / `@(SelectAll:False)`: set to true by default, add this parameter as set to false to only retrieve a single (random) player

These advanced target types may also be combined by comma separating them, e.g. `@(Cat:<Category>,Align:<Alignment>)`  

You can also use a not operation by using `!` after `:`, e.g. to select all non-townsfolk aligned players: `@(Align:!Townsfolk)`  

Within advanced target types, substitute spaces with `-`.

__Limited Target Types__   
These target types are only available in some contexts:
- `@ActionAbilityType`, `@ActionFeedback`: Provides an action's ability type and its feeback in the `On Action` trigger
- `@Members`: Uses all players that are part of the current group or team
- `@Attacker`: Uses the attacker/killers in `On Death`, `On <Target> Death`, `On Killed`, `On Defense` and `On Lynch` triggers
- `@AttackLocation`: Either `@Attacker` or `@AttackSource` depending on which has a corresponding location
- `@AttackSource`: Specifies where the attack came from. Uses the format `<SourceType>:<Source>`, so e.g. `Group:Wolfpack` for a wolfpack attack or `Role:Assassin` for the assassin attack. Available in `On Death`, `On <Target> Death`, `On Killed`, `On Defense` and `On Lynch` triggers. In cases of an attack originating from a poll or choice it specifies the creator of the poll/choice.
- `@DeathType`: Set to the type of death in `On Death` and `On <Target> Death` triggers
- `@KillingType`: Set to the type of killing in `On Defense` and `On <Type> Defense` triggers
- `@This`: Set to the player who died in the `On <Target> Death` trigger or was visited in the `On <Target> Visited` trigger
- `@Visitor`: Set to the visitor in `On Visited` and `On <Target> Visited` triggers
- `@VisitType`: Set to the visiting ability type in `On Visited` and `On <Target> Visited` triggers
- `@VisitParameter`: Set to an additional parameter from the visit in `On Visited` and `On <Target> Visited` triggers
- `@Winner`: Set to the winner of the poll in `On Poll Closed` trigger
- `@Chosen`: Set to the choice chosen in a choice chosing
- `@Voters`: Set to all voters for the winning player in `On Poll Win/Closed`
- `@OtherVoters`: Same as `@Voters`, but excluding `@Self`
- `@ThisAttr`: Set to the current attribute instance when inside an attribute
- `@Chooser`: Set to the player who chose a choice through choice choosing in a choice inside of choice creations
- `@RoleChanger`: Set to the player who caused a role change
- `@Ind`: Set to one player of a selector inside a for each complex action
- `@ActionTarget`: Set to the player an action is used on in `On Action` trigger
- `@ActionResult`: Set to the result of an action in `On Action` trigger
- `@Executor`: Set to a player who voted on a poll in a group which triggers the `On Poll Closed` trigger. Is also set to the executor of the ability instead of the group.

__Team Specific Selectors:__
- `&<TeamName>`: Select a specific team
- `&All`: Selects all teams
- `&Self`: Selects the current team
- `&Ind`: Set to one team of a selector inside a for each complex action 
- `&(Attr:<Attribute>)`: Select all teams with a specific attribute
- `&(Attr:!<Attribute>)`: Select all teams without a specific attribute
- `&(Align:!<Alignment>)`: Select all teams except for a certain alignment

__Role Specific Selectors:__
- `^All`: Selects all teams
- `^(Class:<Class>)`: Select all roles from a specific class
- `^(Team:<Team>)`: Select all roles from a specific team
- `^(Cat:<Category>)`: Select all roles from a specific category
- `^(Type:<Type>)`: Select all roles from a specific type
(all can be inverted with "!")

#### Property Access
When a target type refers to a single role or player, we can use `->` to access a property of the role or player (e.g. `@Target->Role` to get the target's role)

- `<TargetType>->Role`, to get the role of a player target type
- `<TargetType>->Alignment`, to get the alignment of a player target type
- `<TargetType>->Category`, to get the category of a role target type
- `<TargetType>->PublicVotingPower`, to get the public voting power of a player
- `<TargetType>->PrivateVotingPower`, to get the private voting power of a player
- `<TargetType>->Counter`, to get the counter of a game element
- `<TargetType>->Target`, to get the target of a game element
- `<TargetType>->OriginalRole`, to get the original role(s) of a player
- `<TargetType>->Source`, to get the source player of an attribute
- `<TargetType>->Value1`, to get the first custom value of an attribute
- `<TargetType>->Value2`, to get the second custom value of an attribute
- `<TargetType>->Value3`, to get the second custom value of an attribute
- `<TargetType>->Members`, to get the members of a team or group
- `<TargetType>->Players`, to get the players that have a specific role
- `<TargetType>->Attr(<Attribute>)`, to get a certain attribute of a player/team/group
- `<TargetType>->RandomPlayer`, to get a random player out of a selector that may have several players
- `<TargetType>->MostFreqRole`, selects the most frequent role in a list of players or roles
- `<TargetType>->Count`, returns the amount of values inside the target

Property access can be chained such as `@Target->Role->Category`

----
----
### Duration Types

Many abilities apply attributes and need to specify an attribute duration. The following attribute duration types may be used:

- `~Permanent`: Lasts until role loss
- `~Persistent`: Lasts even past role lose
- `~Phase`: Lasts until the end of the current phase
- `~NextPhase`: Lasts until the end of the next phase
- `~NextDay`, `~NextNight`: Lasts until the end of the next day/night
- `~UntilUse`: Lasts until the attribute is used. (For disguises this is when it affects an investigation. For defenses this is when it defends from a killing)
- `~UntilSecondUse`: Lasts until the attribute is used twice
- `~Attribute`: Lasts as long as the originating attribute is applied, can only be used inside an attribute.
- `~UntilUseAttribute`: Lasts until used (see `~UntilUse`) or until the originating attribute disappears (see `~Attribute`)
- `~DelayedPhase`: Starts at the start of the phase and lasts until the end of that phase

----
----
### Ability Type

---
#### Killing

Format: `<Subtype> <Target>`

- Subtypes: `Kill`, `Attack`, `Lynch`, `True Kill`, `Banish` and `True Banish`
- Target: A target type, specifying who is getting killed

---
#### Investigating

Format:  
`<Subtype> Investigate <Target> (<Disguise Level>)` (Investigate role/alignment/category/class) - Returns the Role/Alignment/Category  
`Attribute Investigate <Target> for '<Attribute>' (<Disguise Level>)` (Look if a certain attribute is present) - Returns Success/Failure  
`Investigate <Target> Count (<Disguise Level>)` (Investigates the amount of times a certain role exists)  
`Investigate <Target> Player Count (<Disguise Level>)` (Returns the amount of players inside a target type)  

- Subtypes: `Role`, `Alignment`, `Category` or `Class`
- Target: A target type, specifying who is getting investigated [Must be a player]
- Relevant Effects: A comma separated list of the following two values: `WD` (Weak Disguises) or `SD` (Strong Disguises), listing all of those the investigation is affected by. Leave this out if not affected by any disguises.
- Attribute: An attribute which is getting checked for

---
#### Targeting

Format:  
`Target <Target> (<Type>)`  
`Untarget`

- Target: A target type, specifying what is getting targeted
- Type: Specifies what type of object can be targeted. May be `Player` (a living player besides the player themselves), `Dead` (a dead player), `Role`, `Attribute`, `Category` (e.g. Killing), `Full Category` (e.g. Townsfolk Group) or `Boolean` (True/False). `Player` is the default value and may be left out.

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
- KillingSubtype: `Attacks`, `Kills` (automatically contains attacks), `Lynches`, `Attacks & Lynches` or `All` (attacks/kills/lynches); `Banishments` in haunted mode
- Selector: A target type, limits the protection to only work against certain players.
- Subtype: `Absence at <Location>`, `Active Defense`, `Passive Defense`, `Partial Defense` or `Recruitment Defense`
  - Location: Either a channel (`#channelName`) or a target type
- Phase: `Night` or `Day`, limits the protection to only be active during a certain phase. 
- Duration: A duration type. Defaults to `~Permanent`, leave out if unnecessary.

As a result of a Protecting the <Target> receives a `Defense` attribute.

---
#### Applying

Format:  
`Apply '<Attribute>' to <Target> (<Duration>) (<AdditionalAttributeData>)` (Add a custom attribute)  
`Remove '<Attribute>' from <Target>` (Remove a custom or standard attribute)  
`Change '<Attribute>' value '<Index>' to '<NewValue>' for <Target>` (Change an attributes value)  

- Attribute: (apply) A defined custom attribute, (remove) a defined custom or a standard attribute 
- Target: A target type (a player or a group)
- Duration: A duration type. Defaults to `~Permanent`, leave out if unnecessary.

As a result of an Applying the <Target> receives a custom attribute.

---
#### Redirecting

Format:  
`Redirect '<Subtype>' to <Target> (<Duration>)` (Redirect all player's abilities)  
`Redirect '<Subtype>' from <Source> to <Target> (<Duration>)` (Redirect only specific player's abilities)  

- Subtype: An ability (sub-)type name, or `all` or `non-killing abilities`
- Target: A target type, specifying who to redirect to
- Source: A target type, specifying abilities from which players should be redirected
- Duration: A duration type, specifying for how long the redirect lasts. Defaults to `~Permanent`, leave out if unnecessary.

As a result of a Redirection the current players receives a `Redirection` attribute.

---
#### Vote Manipulating

Format:  
`Manipulate <Target>'s '<Subtype>' to '<Value>' (<Duration>)` (absolute manipulation)  
`Manipulate <Target>'s '<Subtype>' by '<Value>' (<Duration>)` (relative manipulation)  

- Target: A target type, specifying who is being manipulated
- Subtype: `public voting power`, `special public voting power` or `private voting power`
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

Format:  
`Join '<GroupName>' (<Duration>)`  
`Join '<GroupName>' as '<MembershipType>' (<Duration>)`  
`Leave '<GroupName>'`  
`Add <Target> to '<GroupName>' (<Duration>)`  
`Add <Target> to '<GroupName>' as '<MembershipType> (<Duration>)`  
`Remove <Target> from '<GroupName>'`  

- GroupName: The name of a defined group
- MembershipType: Either `Member`, `Owner` or `Visitor`. Defaults to `Member`when not specified.
- Target: A target type, specifying the player that gets added to the group
- Duration: A duration type, specifying for how long the membership lasts. Defaults to `~Persisent`, leave out if unnecessary.

As a result of a Joining the <Target> receives a `Group Membership` attribute.

When attempting to join a group the player is already part of, this fails, unless the new membership is of a higher tier, in which case the previous membership is replaced entirely.

Changing roles automatically leads to a downgrade of all group memberships of member type to memberships of visitor type.

---
#### Granting

Format:  
`Grant '<ExtraRole>' to <Target>` (Grant an extra role)  
`Revoke '<ExtraRole>' from <Target>` (Remove an extra role)  
`Transfer '<ExtraRole>' from <Target> to <Target>` (transfer an extra role, retaining the same secret channel)

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
`Obstruct [<Ability Type>|<Ability Subtype>] for <Target> ⇒ (<Chance>:<Feedback>,<Chance>:<Feedback>) (<Duration>)` (Block a certain ability type and return fake feedback with a certain chance, use `@Result` to refer to the default feedback)   

- Target: A target type, specifying which player should be obstructed
- Ability Type: An ability type, specifying which ability types should be obstructed
- Ability Subtype: The full name of an ability subtype, usually gained from appending the ability type name to the ability subtype name
- Feedback: A custom feedback that should be returned. The feedback is given in the normal format of the specified ability type. Possible values for the `Killing` ability type would be `Failure` or `Success`. Possible values for the `Role Investigation` ability subtype would be any role.
- Duration: A duration type. Defaults to `~Permanent`, leave out if unnecessary.

As a result of an Obstruction the <Target> receives an `Obstructed` attribute.

---
#### Poll Manipulating

Poll Manipulations can affect polls that do not yet exist. For example, a poll manipulation may cancel a lynch during the night, which will cancel the next day's lynch.

Format:  
`Add '<PollType>' Poll` (Duplicate an existing poll)  
`Create '<PollType>' Poll in <Location>` (Creates a poll and the role will execute the resulting ability - for this use `On Poll Closed`)  
`Cancel '<PollType>' Poll` (Cancel the poll's resulting ability)  
`Delete '<PollType>' Poll` (Remove a poll that would otherwise exist)  
`Manipulate '<PollType>' Poll (<Target> is '<ManipulationType>')` (Manipulate a poll's candidates)  
`Manipulate '<PollType>' Poll (<Target> has '<Number>' votes)` (Manipulate a poll's candidates)  
`Manipulate '<PollType>' Poll (<Target> has '<Number>' hidden votes)` (Manipulate a poll's candidates)  

- Poll Type: A type of poll. For example `Lynch`, `Election` or otherwise defined polls.
- Target: A target type, specifying which player should be manipulated on the poll
- ManipulationType: Specifies how the player should be manipulated on the poll:
  - `Unvotable`: The player cannot be voted for on the poll
  - `Disqualified`: The player cannot win the poll, but can be voted for

---
#### Announcements

Format:  
`Reveal [<Target>|<Information>] to [<Location>|<Target>]` (Self Reveal, Public Information)  
`Announce <Information>` (Adds information to storytime buffer)
`Learn '<Information>'` (Private Information)  
`Know '<Information>'` (Private Knowledge)  

- Target: A target type, specifying what to reveal. This may be a player or a piece of information.
- Location: A channel to reveal information too
- Information: (reveal) Text that is revealed, (learn, know) Text the player receives

---
#### Changing

Format:  
`Role Change <Target> to '<Role>'` (Change a player's role)  
`Alignment Change <Target> to '<Alignment>'` (changes a player's alignment - only valid of unaligned players)  
`Group Change <Target> to '<Group>'` (Change a group's name, but not member roles)  

- Target: A target type, specifying which player's role to change
- Role: A target type or constant role, specifying the player's new role

---
#### Copying

Format:   
`Copy <Target>` (Copies all values of one player to yourself, including role, attributes, targets, counters, etc)  
`Copy <Target> (Suppressed)` (Copies all values of one player to yourself, including role, attributes, targets, counters, etc, but stops starting abilities from triggering)  

- Target: A target type, specifying a role or player to copy from

---
#### Choices

Choice Creation Format:
```
'<Name>' Choice Creation [for <Target|Location>] (Option List)
Choice <Option Name> Chosen:
  • <Resulting Ability>
  • <Resulting Ability>
Choice <Option Name> Chosen:
  • <Resulting Ability>
```

- Name: Gives the choice a name to reference later
- Target|Location: Optional, specifies to which player, target type or location the choice should be sent. If not specified the current player chooses.
- Option List: A comma separated list of options
- Option Name: The names for each option which are what can be selected by choice choosing
- Resulting Ability: Abilities that result after a choice choosing, executed by the role who created the choice. Use `@Chooser` to reference the person who choice chose here

Choice Choosing Format:
```
'<Name>' Choice Choose '<Option Name>'
```

- Name: The choice to choose one
- Option Name: The name of the chosen option. You may use `@Chosen` here. A choice may be submitted with a `@Selection` and a `@SecondarySelection`

---
#### Ascend Descend

Format:  
`Ascend`  
`Descend`  

Both ascend/descend automatically also true kill. 

---
#### Disband

Format:  
`Disband` (disband current group)  
`Disband <Target>`

`Disband` disbands the current group if used within a group. Otherwise use `Disband <Target>` and specify a group to be disbanded.

---
#### Counting

Each game element (role, group, team, etc) has a counter. The counting ability type manipulates this counter.

Format:  
`Increment Counter` (increments own counter)  
`Decrement Counter` (decrements own counter)  
`Increment Counter by <Value>` (increments own counter)  
`Decrement Counter by <Value>` (decrements own counter)    
`Set Counter to <Value>` (set own counter)   
`Increment Counter for <Target>` (increments a counter)  
`Decrement Counter for <Target>`(decrement a counter)  
`Increment Counter for <Target> by <Value>` (increments a counter)  
`Decrement Counter for <Target> by <Value>`(decrement a counter)  
`Set Counter to <Value> for <Target>` (set a counter)  

- Target: A target type, specifying who's counter to manipulate
- Value: A numeric value specifying a new counter value. Value may additionally also instead be of the following format: `[ceil|floor|round] <variable>/<number>`.

---
#### Conversation Reset

Removes all messages from a group or extra role.

Format:  
`Conversation Reset`  
`Conversation Reset <Target>`    

- Target: A target specifying what to reset. Can also directly be a `'Group Name'`. When no target is specified resets the current group or extra role.

---
#### Cancel

Within `On Visited` triggers you can use `Cancel` to cancel the visit.

Format:  
`Cancel` (Cancels the visit with a resulting failure)  
`Cancel with [Failure|Success|'<Custom Feedback>']` (Cancels the visit with specific feedback)  

---
#### Switching

Format:  
`Switch with <Target>`  

Does a thief switch with another player.
- Target: A target specifying who to switch with

---
#### Shuffle

Format:  
`Shuffle <Target> <Target>`  

Shuffle 2-5 targets
- Target: A target

---
#### Emitting

Format:  
`Emit 'Value'`  
`Emit 'Value' for <Target>`  

Emits a value for a `On Emitted` trigger.

---
#### Displaying

Format:  
`Display 'DisplayName'`  
`Display 'DisplayName' (Value1)`  
`Display 'DisplayName' (Value1, Value2)`  
`Display 'DisplayName' (Value1, Value2, Value3)`  
`Display 'DisplayName' (Value1, Value2, Value3, Value4)`  
`Update 'DisplayName' value 'Index' to 'NewValue'`  

Creates or updates a display message. Value may be any string, but "yes"/"no" are interpreted as the respective emoji, "counter" is interpreted as counter value of the current element and "target" is interpreted as the target of the current element.

## Host Information

Host Information is specific information provided by the host and is part of the game setup, same as the role list.

Host Information comes in the following forms:

- `%Role%`, an arbitrary role
- `%Role:<Target>%`, an arbitrary role from a provided target type
- `%Player%`, an arbitrary player
- `%Player:<Target>%`, an arbitrary player from a provided target type
- `%Option1|Option2|Option3%`, one arbitrary option out of several options
- `%PartialRoleList%`, contains an arbitrary amount of information about the role list

Use `%Role1%`, `%Role2%` / `%Player1%`, `%Player2`, etc when refering to several different host informations of the same type.
