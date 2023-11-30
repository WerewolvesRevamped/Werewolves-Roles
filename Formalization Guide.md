# A guide on how to formalize roles/groups/teams/attributes

## Contents

- [Basics](#basics)
- [Role](#role)
- [Abilities](#abilities)
  - [Ability](#ability)
  - [Target Types](#target-types)
  - [Duration Types](#duration-types)
  - [Ability Type](#ability-type)
    - [Killing](#killing)
    - [Investigating](#investigating)
    - [Targeting](#targeting)
    - [Disguising](#disguising)

## Basics
- `<Argument>`: Should be replaced by something. E.g. `<Argument>` ⇒ `Example`  
- `[<Argument1> | <Argument2>]`: Should be replaced by one of the options within `[]` which are separated by `|`, e.g. `[<Argument1> | <Argument2>]` ⇒ `<Argument1>` ⇒ `Example1`  
- `[<Argument1> | Text1 | Text2]`: Should be replaced by one of the options within `[]`, e.g. `[<Argument1> | Text1 | Text2]` ⇒ `Text1`

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

### Ability
Single Ability
```
<Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
```
Multiple Abilities
```
<Trigger Type>:
  - <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
  - <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
```
The three elements `[<Action Restriction>]`, `{<Action Compulsion>}` and `<<Action Scaling>>` should be left out if unnecessary.

If a single trigger does several abilities at once, they can be listed as part of one trigger as shown above.

- Trigger Type: One of the following:
  - An Action Timing (`Start Night`, `End Night`, `Start Day`, `End Day`, `Immediate Night`, `Immediate Day`, `End Phase`, `Immediate`) if the ability occurs in connection to a non-compund action
  - `Compound` **WIP**
  - `Starting` for starting abilities
  - `Passive` for constantly active abilities
- Action Restrictions: One of the following:
  - Temporal Restriction: **WIP**
  - Attribute Restriction: **WIP**
  - Succession Restrictions: **WIP**
- Action Scaling: **WIP**
- Action Compulsion: `Forced` for forced actions, blank otherwise

- Ability Type: Specifies which ability type and how to execute it


### Target Types

Within abilities a selection by the player often affects the execution of the abilities. We use target types to refer to the player, their selection or similar. The following target types exist:

- `@Self`: Uses the player who this ability belongs to
- `@Selection`: Uses the player/role/etc selected in the action
- `@SecondarySelection`: Uses a secondary selection from the action (e.g. "Disguise <Selection> as <SecondarySelection>")
- `@Target`: Uses the current target set by the player

### Duration Types

Many abilities apply attributes and need to specify an attribute duration. The following attribute duration types may be used:

- `~Permanent`: Lasts until role loss
- `~Persistent`: Lasts even past role lose
- `~Phase`: Lasts until the end of the current phase
- `~NextDay`, `~NextNight`: Lasts until the end of the next day/night

### Ability Type

---
#### Killing

Format: `<Subtype> <Target>`

- Subtypes: `Kill`, `Attack`, `Lynch` and `True Kill`
- Target: A target type

---
#### Investigating

Format: `<Subtype> Investigate <Target> (<Relevant Effects>)`

- Subtypes: None
- Target: A target type
- Relevant Effects: A comma separated list of the following three values: `WD` (Weak Disguises), `SD` (Strong Disguises), `OB` (Obstructions), listing all of those the investigation is affected by

---
#### Targetting

Format: `Target <Target>`

- Target: A target type

---
#### Disguising

Format: `<Subtype> Disguise <Target> as '<Role>' (<Duration>)`

- Subtype: `Weakly` or `Strongly`
- Target: A target type
- Role: A role
- Duration: A duration type

---
#### Protecting

Format: `Protect <Target> from <KillingSubtype> by <Subtype> (<Duration>)`

- Target: A target type
- KillingSubtype: One of the subtypes of the killing ability.
- Subtype: `Absence at <Location>`, `Active`, `Passive` or `Partial`
  - Location: Either a channel (`#channelName`) or a target type
- Duration: A duration type

---
#### Applying

Format:
- `Apply <Attribute> to <Target> (<AdditionalAttributeData>)`
- `Remove <Attribute> from <Target>`

- Attribute: A defined custom attribute
- Target: A target type
- Additional Attribute Data: A comma separated list of additional attribute data defined by the attribute

---
#### Redirection

Format: `Redirect <Subtype> to <Target>`

- Subtype: An ability type name, or `all` or `non-killing abilities`
- Target: A target type

#### Vote Manipulating

Format: `Manipulate <Target>'s <Subtype> to '<Value>'`

- Target: A target type
- Subtype: `public voting power`, `private voting power` or `public starting votes`
- Value: A target type

---
#### Whispering

Format: `Whisper to <Location> as <Disguise> (<Duration>)`

- Location: Either a channel (`#channelName`) or a target type
- Disguise: A role, or if none, remove `as <Disguise>`
- Duration: A duration type

---
#### Joining

Format: `Join <GroupName>`

- GroupName: The name of a defined group

---
#### Granting

Format:
- `Grant '<ExtraRole>' to <Target>`
- `Revoke '<ExtraRole>' to <Target>`

- ExtraRole: A defined extra role
- Target: A target type

---
#### Loyalty

Format: `Loyalty to <Target> (<Subtype>)`

- Target: The name of a defined group (if subtype is group) or a defined team (if subtype is alignment)
- Subtype: `Group` or `Alignment`
