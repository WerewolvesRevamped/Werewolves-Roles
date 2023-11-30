# A guide on how to formalize roles/groups/teams/attributes

## Basics
- `<Argument>`: Should be replaced by something. E.g. `<Argument>` ⇒ `Example`  
- `[<Argument1> | <Argument2>]`: Should be replaced by one of the options within `[]` which are separated by `|`, e.g. `[<Argument1> | <Argument2>]` ⇒ `<Argument1>` ⇒ `Example1`  
- `[<Argument1> | Text1 | Text2]`: Should be replaced by one of the options within `[]`, e.g. `[<Argument1> | Text1 | Text2]` ⇒ `Text1`  

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
```
<Trigger Type>: <Ability Type> [<Action Restriction>] {<Action Compulsion>} <<Action Scaling>>
```
The three elements `[<Action Restriction>]`, `{<Action Compulsion>}` and `<<Action Scaling>>` should be left out if unnecessary.

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

### Ability Type

  
