# Formalization Guide

- [Introduction](#introduction)
- [Game Elements](#game-elements)
- [Types](#types)
  - [Player Type](#player-type)
  - [Role Type](#role-type)
  - [Active Extra Role Type](#active-extra-role-type)
  - [Group Type](#group-type)
  - [Alignment Type](#alignment-type)
  - [Location Type](#location-type)
  - [Poll Type](#poll-type)
  - [Success Type](#success-type)
  - [Result Type](#result-type)
  - [Info Type](#info-type)
  - [Ability Type Type](#ability-type-type)
  - [Ability Subtype Type](#ability-subtype-type)
  - [Ability Category Type](#ability-category-type)
  - [Number Type](#number-type)
  - [Boolean Type](#boolean-type)
  - [Attribute Type](#attribute-type)
  - [Active Attribute Type](#active-attribute-type)
  - [Display Type](#display-type)
  - [Display Value Type](#display-value-type)
  - [Category Type](#category-type)
  - [Killing Type Type](#killing-type-type)
  - [Class Type](#class-type)
  - [Source Type](#source-type)
  - [Option Type](#option-type)
  - [String Type](#string-type)
  - [Null Type](#null-type)
  - [Variables](#variables)
- [Sources](#sources)
- [Triggers](#triggers)
- [Game Element Formats](#game-element-formats)
  - [Roles Format](#roles-format)
  - [Teams Format](#teams-format)
  - [Groups Format](#groups-format)
  - [Abilities Format](#abilities-format)

## Introduction
Werewolves Revamped is automated using "formalization" - i.e. all roles and similiar are written in a formal way in a custom language (WWRF) which can be interpreted by the bot.

WWRF is designed with a "Trigger > Condition > Action" model: there are certains events that emit a "trigger" (a phase starting, a player being killed, etc) and there are game "actions" which change the game state and my emit triggers themselves (e.g. killing a player, creating a group, manipulating voting values). All elements in WWR are designed by combining triggers and actions (and optionally including conditions that limit the execution of the action to certain cases). Actions are differentiated between prompting actions, which prompt the player for an input and are only executed once such an input is provided and automatic actions which are executed without player interaction (and potentially even without the players knowledge)

For example, the Fortune Teller has a prompting investigation actions which is triggered by the night starting. The Wolf Cub has an automatic poll creating action which is triggered by their death.

## Game Elements

Game Elements are all the components that make up the game. This includes players, roles, groups, polls, teams and more.

We differentiate between active and passive game elements. Active elements can be modified, while passive elements remain static. Passive elements are what is defined in this repository, and active elements are instances of a passive element created during the game. Outside a game only passive elements exist. Some game elements exist in both passive and active form.

For example, the concept of a lynch poll, as defined in this repository is a passive game element, whereas one specific lynch poll within a game is an active game element.
Passive game elements store general information about the element (e.g. a role's description), whereas active elements store game data (e.g. a player's date).

Furthermore we differentiate between acting active elements and non-acting active elements. Acting elements are those that are able to execute (i.e. create) abilities.

For example, a player can create an ability by using their role's abilities. A display, on the other hand, cannot create or execute abilities.

The following types of game elements exist:

Name | Passive  | Active | Acting | Notes
--- | --- | --- | --- | --- 
Roles | ✅ | ⛔ | ⛔ | ∗ 
Players | ⛔ | ✅ | ✅ |  
Teams | ✅ | ✅ | ✅ | ⁑ 
Groups | ✅ | ✅ | ✅ |  
Polls | ✅ | ✅ | ✅ | ⁂
Attributes (Default) | ⛔ | ✅ | ⛔ |  
Attributes (Custom) | ✅ | ✅ | ✅ |  
Attributes (Role) | ⛔ | ✅ | ✅ | 
Abilities | ⛔ | ✅ | ⛔ |  
Displays | ✅ | ✅ | ⛔ |  
Locations | ✅ | ⛔ | ⛔ |  
Choices | ⛔ | ✅ | ⛔ |  

∗ Roles can be instantiated in two ways: when they are assigned to a player, the player sort of becomes the instantiated version of the role, though of course there is more data on the player. Alternatively if a role is assigned as an extra role mid-game it is instantiated as a role type attribute.  
⁑ While Teams are both active and passive, they are not instantiated as there can only ever be one of each team. Instead the team's active and passive data is stored in the same element.
⁂ While Polls are both active and passive, and while they are instantiated, their data is still stored on the passive poll, meaning that if a poll updates its counter or target it will affect all polls of the same type.

The format of various game elements are described in more detail below.

Active game elements generally always support two values that can be modified and accessed through various methods:
- Target: A value that can store a variety of types and can be modified through targetting.
- Counter: A numeric value that can be modified through counting.

## Types

In many cases roles or other game elements need to deal with values, for example player inputs or constants used for comparisons. Each value has a certain type which must be defined or inferred.

For example, a role investigation takes one input value: a player that is to be investigated. A disguise takes two inputs: a player that should be disguised and a role they should be disguised as. These inputs may be player submissions, but may also be builtin to the role (for example for a Tanner both the role and the player are user submitted, but for a Disguised Fox the player is always set to themselves, whereas the role is user submitted).

Each value needs to be annotated with its respective type, however most of the time the WWRF parser can do this - in the investigation example the target must always be a player, so the value is always annotated as a player type. For some other abilities, this is less clear, however. In these cases the type can sometimes be inferred from the contents of the value itself (sometimes at parse time, sometimes at runtime) and other times the type has to be manually annotated. Check for each ability what types it expects and when annotation may be necessary. Annotation is done be appending the type surrounded by square brackets, for example: ``​`Citizen`[role]``.

Values are split into two categories: constant values (e.g. `Citizen`, referring to the citizen role) or selectors (e.g. `@Self`, referring to the current game element). Furthermore, there is a differentiation between basic selectors (e.g. `@Self` or `@Target`) which refer to a specifc value based on context and advanced selectors (e.g. `@(Role:Citizen)`, returing all players with the citizen role) which select values according to a specified query.

This is a list of values and example constant values and selectors:

Value Type | Constant Example  | Basic Selector Example | Advanced Selector Example | List Type
--- | --- | --- | --- | ---
Player | ⛔ ∗ | @Self | @(Role:Citizen) | ✅
Role | `Citizen` | @Target | ^(Team:Townsfolk) | ✅
Active Extra Role | `Mayor` | @ThisAttr | ⛔ | ✅
Group | #Bakers | @Self | ⛔ | ⛔
Alignment | Townsfolk | &Self | &(Align:!Townsfolk) | ✅
Location | ⁑ | ⁑ | ⁑ | ⛔
Base Location | #tavern | ⛔ | ⛔ | ⛔
Poll | `Wolfpack` | @Self | ⛔ | ⛔
Success | Success | ⛔ | ⛔ | ⛔
Result ⁂ | ⛔ | @Result | ⛔ | ⛔
Info | `Attacked @Target` ⁑⁑ | @ActionFeedback | ⛔ | ⛔
Ability Type | `Killing` | @VisitType | ⛔ | ⛔
Ability Subype ⁑⁑∗ | `Attack Killing` | @VisitSubtype | ⛔ | ⛔
Ability Category | `All` | ⛔ | ⛔ | ⛔
Number | 1 | @Selection | ⛔ | ⛔
Boolean | True | ⛔ | ⛔ | ⛔
Attribute | `Wolfish` | @VisitParameter | ⛔ | ✅
Active Attribute | `Marker` | @ThisAttr | `Marker:Self` | ✅
Display | `Potions` | ⛔ | ⛔ | ⛔
Display Value | `Counter` | ⛔ | ⛔ | ⛔
Category | `Killing` | ⛔ | ⛔ | ✅
Killing Type | `Attack` | @DeathType | ⛔ | ⛔
Class | `Townsfolk` | @Result | ⛔ | ✅
Source | `Group:Wolfpack` | @AttackSource | ⛔ | ✅
Option | `Join Pack` | @Option | ⛔ | ✅
String | `Text` | ⛔ | ⛔ | ✅
Null | ⛔ | ⛔ | ⛔ | ✅

∗ Players can not be referenced using a constant value, as that would require this player to be present in every game.  
⁑ Locations are a special type which unifies several other types into a single type. The following types can be interpreted as a location: group, base location, active extra role, player, attribute.  
⁂ Results are a special type which is returned after executing an ability, depending on context different types can be extracted from it. It always stores at least a success type (for if the ability succeeded) and an info type (for the ability feedback) and depending on the ability potentially other abilities.  
⁑⁑ Info is a special type which is a text containing several selectors. All selectors contained within the info text must either be annotated or support run-time annotation. The info type is used when a text output is generated. As part of the text output process all selectors contained within the info type are evaluated and converted to text.  
⁑⁑∗ An Ability Subtype contains the info of the ability type _and_ the subtype, not just the latter.  

Some types additionally support property accesses using the `<Selector>-><Property>`, e.g. `@Target->Role` retrieves a targets role.

Many types default to not just being a single value, but actually a list, though this will often be a list of length one, this is shown in the table above. Types that are list types will return several results if a selector matches several results (though some abilities may choose to only use the first element of the list), while types that are not list types can only return a single value. When using a property access on a list type, the property access is executed on each element in the list and a new list is returned.

### Player Type

Player type is one of the most common types and refers to a player of the game (active). This type is also known under `player_attr` (referring to a player when acting through an extra role) or `player_group` (referring to a player when acting through a group).

Selector | Meaning
--- | ---
@Self | The current player (Alternatively, the player who an attribute is applied on when using from an attribute).
@All | All living players.
@Others | @All without @Self.
@Dead | All dead players.
@DeadAlive | All players.
@Target | The current player's target (must be alive).
@TargetDead | The current player's target (even if the target is dead).
@Members | The current group's or team's members.
@Attacker | A player responsible for a killing in `On Death`, `On Killed`, `On Banishment`, `On Banished` and variants.
@This | Refers to the player for which a complex trigger featuring a target (e.g. `On <Target> Death`) triggered for.
@Winner | Refers to the winner of a poll in `On Poll Closed`.
@ActionTarget | Refers to the target of an action in `On Action` and variants.
@Executor | Refers to the executor of an action in `On Poll Closed` for polls created by a group.
@Selection | Refers to a selection submitted by a player through a prompt.
@SecondarySelection | Refers to a selection submitted by a player through a prompt.
@RoleChanger | Refers to the responsible player in `On Role Change`.
@Chooser | Refers to the player that made the choice in `Choice Chosen`.
@Visitor | Refers to the visiting player in `On Visited`, `On Redirect` and variants.
@Joiner | Refers to the joining player in `On Join`.
@VisitParameter | Refers to a parameter in a visit in `On Visited`, though this will usually __NOT__ be a player.
@Ind | Refers to the current element in a `For Each` ability.
@Voters | Refers to all players that voted for the winning option in `On Poll Closed` and `On Poll Win`.
@OtherVoters | @Voters without the poll winner.
@Result[1-7] | Refers to the result of a processed ability, which will be cast to a player if possible.
@ActionResult | Refers to the result of an action in `On Action` and variants.
@ID:\<ID\> | Refers to a specific player based on discord id. Useful for testing.
%Player[N]% | Refers to a player stored as host information.

The advanced player selector has the format @(Property:Value) and searches for players where a certain property matches a certain value. For example, `@(Role:Citizen)` will return all players who's role is `Citizen`. All properties may be inverted using an `!` at the start of the value, e.g. `@(Role:!Citizen)` will return all players who's role is __not__ `Citizen`.

Advanced Selector Value | Meaning
--- | ---
Role | The player's current role.
Cat/Category | The player's current role's category.
Class | The player's current role's class (most commonly Townsfolk/Werewolf/Solo/Unaligned/Extra).
Align/Alignment | The player's current alignment. A player can either align with a team, in which case their alignment is their team or they can be nonaligned in which case their alignment is set to `unaligned`.
FullCat | Matches both Class and Category (e.g. `@(FullCat:Townsfolk-Power)`), must be `-` separated.
OrigRole | The player's original role.
OrigCat | The player's original role's category.
OrigClass | The player's original role's class.
OrigAlign | The player's original role's default alignment (it should be noted that this may differ even without a role change necessary as `Align` checks for the players current alignment, not the player's current role's default alignment).
OrigFullCat | Matches both class and category of the player's original role.
Group | Checks for membership of a certain group.
Attr/Attribute | Checks whether the player has a certain custom attribute.
AttrSelf | Checks whether the player has a certain attribute which was applied by the current player.
AttrRole | Checks whether the player has a certain role type attribute.
AttrDisguise | Checks whether the player has a disguise applied by a certain player. As value specify a player selector which will have `@` prepended, so e.g. just `Self`.
AliveOnly | Special field. Set it to `False` to also search for dead players.
SelectAll | Special field. Set it to `False` to return a single random player from the selector instead of all players.

Players support a variety of property accesses:

Property | Meaning
--- | ---
Role | The player's current role.
Category | The player's current role's category.
OriginalRole | The player's original role.
Alignment | The player's current alignment.
Target | The player's target.
Counter | The player's counter.
PublicVotingPower | Evaluates the player'S current public voting power.
PrivateVotingPower | Evaluates the player'S current private voting power.
RandomPlayer | Selects a random player from a list of players (e.g. `@All->RandomPlayer` would return an entirely random player).
MostFreqRole | Returns the most common role amongst a group of players (e.g. `@(Group:Wolfpack)->MostFreqRole` would return the most common role in the wolfpack).
Attr(\<AttributeName\>) | Returns a certain custom attribute that is applied to the player. 
Count | Returns the amount of players (e.g. `@All->Count` returns the amount of living players).

### Role Type

Role type is one of the most common types and refers to a role (passive).

Selector | Meaning
--- | ---
@Target | The current player's target (must be alive).
@Result[1-7] | Refers to the result of a processed ability, which will be cast to a role if possible.
@ActionResult | Refers to the result of an action in `On Action` and variants.
@VisitParameter | Refers to a parameter in a visit in `On Visited`.
@Option | Refers to the chosen option of a choice, though this will be an option type it will be cast to a role if possible.
@Selection | Refers to a selection submitted by a player through a prompt.
@SecondarySelection | Refers to a selection submitted by a player through a prompt.
^All | Refers to all roles
``​`<RoleName>`​`` | Constant role 
%Role[N]% | Refers to a role stored as host information.

The advanced role selector has the format ^(Property:Value) and searches for roles where a certain property matches a certain value. For example, `^(Cat:Killing)` will return all killing roles. All properties may be inverted using an `!` at the start of the value, e.g. `^(Team:!Townsfolk)` will return all roles who's are __not__ part of townsfolk.

Selector | Meaning
--- | ---
Cat/Category | The role's category.
Type | The role's type (most commonly Default and Limited).
Class | The role's class.
Team | The role's team.
Count | Returns the amount of roles.

Roles support a few property accesses:

Selector | Meaning
--- | ---
Category | The role's category.
Class | The role's class.
Team | The role's team.
Type | The role's type.
Players | All players that have this role.
Count | Returns the amount of roles.

### Active Extra Role Type

Active Extra role type refers to a special subtype of attributes: role type attributes. These are rarely treated as actual attributes however. Active Extra Roles are created through grantings and show up as an additional secret channel for said role. For example, when a player is elected as a Mayor they receive Mayor as an active extra role.

Selector | Meaning
--- | ---
@ThisAttr | Refers to the current active extra role.
``​`<RoleName>`​`` | Refers to an active extra role name with the specified name which was __created by the current game element__. 

### Group Type

Group types refer to the active instance of a group. Currently there is only the active group type, though when a group name is passed and no active group exists for this group yet, some contexts may automatically create an active instance of the group so the group type is able to return an active instance.

Selector | Meaning
--- | ---
@Self | The current group.
`#<GroupName>` | A group with the specified name.

Groups support a few property accesses:

Selector | Meaning
--- | ---
Target | The group's target.
Counter | The group's counter.
Members | All members of the group.
Attr(\<AttributeName\>) | Returns a certain custom attribute that is applied to the group. 
Count | Returns the amount of groups.

### Alignment Type

Alignment type refers to a team. Teams are both passive/active at the same time.

Selector | Meaning
--- | ---
@Result[1-7] | Refers to the result of a processed ability, which will be cast to a team if possible.
@ActionResult | Refers to the result of an action in `On Action` and variants.
@VisitParameter | Refers to a parameter in a visit in `On Visited`.
@Option | Refers to the chosen option of a choice, though this will be an option type it will be cast to a team if possible.
&All | All teams.
&Self | The current team.
&Ind | Refers to the current element in a `For Each` ability.
``​`<TeamName>`​`` | Constant alignment. 

The advanced team selector has the format &(Property:Value) and searches for teams where a certain property matches a certain value. For example, `&(Attr:Marker)` will return all teams with the marker attribute. All properties may be inverted using an `!` at the start of the value, e.g. `&(Align:!Townsfolk)` will return all teams except townsfolk.

Selector | Meaning
--- | ---
Align/Alignment | The team - only useful when inverted.
Attr | Checks whether the team has a certain custom attribute.

Alignments support a few property accesses:

Selector | Meaning
--- | ---
Target | The team's target.
Counter | The team's counter.
Members | All members of the team.
Attr(\<AttributeName\>) | Returns a certain custom attribute that is applied to the team. 
Count | Returns the amount of teams.

### Location Type

Location type is a special type that combines groups, players, base locations, active extra roles and attributes into one. While the other types exists also outside the location type, base locations are only available within this type.

Selector | Type | Meaning
--- | --- | ---
`#<LocationName>` | Base Location | Name of a base location, such as town square.
`#<GroupName>` | Group | Name of a group.
``​`<RoleName>`​`` | Active Extra Role | Refers to an active extra role name with the specified name which was __created by the current game element__. 
@Self | Player/Group | Refers to the current player (for players, active extra roles), to the player the current attribute is attached to (for attributes) or to the current group (for groups).
@AttackLocation | Player/Group | Either `@Attacker` or `@AttackSource` depending on which can be resolved to a location.
Any Player Selector | Player | If none of the previous selectors matches the specified selector, the value is treated as a player type - all player selectors are valid.

Locations support a property result __only__ when they are a group type location, in which case they support normal group type property accesses.

### Poll Type

Poll type refers to the passive poll. There is no selector for active polls.

Selector | Meaning
--- | ---
``​`<PollName>`​`` | Constant poll.
@Self | Retrieves the name of the current poll.

### Success Type

Success type represents whether an ability succeeded or not. It can take exactly two values: ``​`Failure`​`` and ``​`Success`​``.

### Result Type

Result type is a special type that encompasses several other values. In some contexts it will automatically be cast to one of its values, but generally in most contexts it is necessary to use property access to retrieve one of the values making up the result. Results are returned by an ability when storing its result through a Process ability.

Selector | Meaning
--- | ---
@Result[1-7] | Refers to the result of a processed ability.
@ActionResult | Refers to the result of an action in `On Action` and variants.

Results support a couple of property accesses, however not all properties are present in each result:

Selector | Meaning
--- | ---
Class | A role's class returned as a result.
Category | A role's category returned as a result.
Alignment | A role's team returned as a result.
Role | A role returned as a result.
Result | The result's result as a single value which may take several types.
Success | Whether the ability succeeded (always present).
Target | The primary target of an ability.
Message | The ability feedback a player would receive (always present).
Count | Returns the amount of results.

### Info Type

Info type is a special type - it is plain text including selectors. The selectors are parsed into text if possible, for example: ``​`Your current target is @Target`​`` would inform a player of their current target. For selectors to be parsed into text correctly they must be surrounded by spaces in both directions so that they can correctly be identified.

Selector | Meaning
--- | ---
@ActionFeedback | The feedback of a processed ability.
%PartialRoleList% | Refers to a role list info text stored as host information.
``​`<Info>`​`` | Normal info, potentially containing other selectors.

Each type uses a different method to get parsed to text - for discord elements a respective discord reference is generated (ping, channel link) while other types are usually just their name in title case.

### Ability Type Type

Ability Type type is a type that represents an ability type. Ability types have formal names which may vary from their common names (e.g. `Investigating`).

Selector | Meaning
--- | ---
@VisitSubtype | The ability type of a visit in `On Visited`.
``​`<AbilityType>`​`` | The name of an ability type.

### Ability Subtype Type

Ability Subtype type is a type that represents the exact ability subtype. This is made up as a combination of the ability subtype and the ability type (e.g. `Attack Killing`), with the subtype coming first followed by the type. Subtype that are comprised of multiple words must have their spaces replaced by `-`'s (e.g. `True Kill Killing`).

Selector | Meaning
--- | ---
@VisitType | The ability type of a visit in `On Visited`.
@ActionAbilityType | The ability type of an ability in `On Action`.
``​`<AbilityType>`​`` | The name of an ability type.

### Ability Category Type

Ability Category type is a type for categories of abilities, but currently only supports two values: ``​`all`​`` (which matches all abilities) and ``​`non-killing abilities`​`` (which matches all abilities besides killing).

Selector | Meaning
--- | ---
@VisitType | The ability type of a visit in `On Visited`.
@ActionAbilityType | The ability type of an ability in `On Action`.
``​`<AbilityType>`​`` | The name of an ability type.

### Number Type

Number type is used to represent numbers.

Selector | Meaning
--- | ---
@Selection | Refers to a selection submitted by a player through a prompt.
@SecondarySelection | Refers to a selection submitted by a player through a prompt.
\<Number\> | A number.
%Number[N]% | Refers to a number stored as host information.
\<Variable\> | A [variable](#variables) that is evaluated to a number.
\<Number\>/\<Number\> | A division of two numbers (both of which can be one of the above options), rounded to the nearest full number.

### Boolean Type

Boolean type represents whether something is true or not. It can take exactly two values: `​True` and `False`.

### Attribute Type

Attribute type refers to a _passive_ attribute.

Selector | Meaning
--- | ---
@VisitParameter | Refers to a parameter in a visit in `On Visited`.
``​`<AttributeName>`​`` | The name of an attribute.

### Active Attribute Type

Active Attribute type is the counterpart to the attribute type which refers to active attribute instances. To be able to correctly resolve some types of active attribute selectors as "on element" is required (the element on which the attribute is applied). What is passed here is based on the ability. There are various variants of advanced active attribute selectors, so they are all listed in the same taböle.

Selector | Meaning
--- | ---
@ThisAttr | Refers to the current attribute.
``​`<AttributeName>`​`` | Searches for a custom attribute 
``​`<AttributeName>:Self`​`` | Searches for a custom attribute, applied by the current player.
``​`<AttributeName>:<SourceReference>`​`` | Searches for a custom attribute, applied by a certain source specified by its reference. This is not very usable most of the time as most source references are different each game, however team names can be used. See [sources](#sources).
``​`<AttributeName>:<SourceName>`​`` | Searches for a custom attribute, applied by a certain source specified by its name. See [sources](#sources).
``​`<AttributeName>::<Val1>`​`` | Searches for a custom  attribute, with a specific value in Value 1.
``​`<AttributeName>:Self:<Val1>`​`` | Combination of the above.
``​`<AttributeName>:<SourceReference>:<Value1>`​`` | Combination of the above.
``​`<AttributeName>:<SourceName>:<Value1>`​`` | Combination of the above.
``​`<GenericAttributeType>`​`` | Searches for generic attributes of the specified type. Valid generic attribute types are the following: disguise, defense, absence, manipulation, groupmembership, obstruction, pollcount, pollresult, polldisqualification, pollvotes, role, redirection, loyalty, whisper.
``​`<GenericAttributeType>:Self`​`` | Combination of the above.
``​`<GenericAttributeType>:<SourceReference>`​`` | Combination of the above.
``​`<GenericAttributeType>:<SourceName>`​`` | Combination of the above.
``​`<GenericAttributeType>:<Value1>`​`` | Combination of the above.

Active attributes support a few property accesses:

Selector | Meaning
--- | ---
Target | The attribute's target.
Counter | The attribute's counter.
Source | The attribute's creator player. Fails if not created by a player.
Value1 | The attribute's value1 which may hold arbitrary data.
Value2 | The attribute's value2 which may hold arbitrary data.
Value3 | The attribute's value3 which may hold arbitrary data.
Count | Returns the amount of attributes.

### Display Type

Display types can only be a constant value referring to the display name.

### Display Value Type

Display Value types are used for the values displayed in a display. All text values are supported (and will be displayed as text), but some special values have special meaning.

Value | Meaning
--- | ---
Yes | The yes emoji.
No | The no emoji.
Counter | The counter of the display's creator.
Target | The target of the display's creator.
\<AnyText\> | Just the text.

### Category Type

Category types represent the category of a role. Supported values are any text, but automatic type annotation is only done for known categories (e.g. `Miscellaneous`).

### Killing Type Type

Killing type represents the type of a killing. 

Selector | Meaning
--- | ---
@DeathType | Set to the type of killing in `On Death`.
@KillingType | Set to the type of killing in `On Killed`.
``​`<KillingType>`​`` | A constant killing type. Supported values are: attack, kill, lynch, true kill, banish, true banish.

### Class Type

Class type refers to a role's class. Supported values are any text, but automatic type annotation is only done for known classes (e.g. `Limited`).

### Source Type

Source type is one of the rare instances where the formalization system can directly interact with sources (See [sources](#sources). The source type represents the source name (e.g. `Role:Assassin` or `Group:Wolfpack`).

Selector | Meaning
--- | ---
@AttackSource | Returns the source of an attack in `On Death` and `On Killed`. For groups this will differ from the attacker.
``​`<SourceNameType>:<SourceNameValue>`​`` | A colon separated two segement value, with the first specifying the source type (e.g. `Role`) and the second the name/value (e.g. `Assassin`).

### Option Type

Option type is the value for/from choices.

Selector | Meaning
--- | ---
@Chosen | Special selector which should only be used in the choice choosing ability as it generates a choice prompt with which @Chosen is filled.
@Option | The chosen option in `Choice Chosen`.
``​`<OptionName>`​`` | The name of an option.

### String Type

String type represents an arbitrary text. Can take any value, for example ``​`This is a text.`​``.

### Null Type

Null type is a special type that should not be used manually. When inferring the type of a target at runtime, but no target is set, a null type list with zero elements is instead returned. This is to ensure that abilities relying on a target gracefully fail instead of erroring. Null type always takes the form of an empty list.

Technically null types also support property accesses, however each property access will simply return an empty list as well.

### Variables

Variables aren't directly a type, but are used in some contexts. When a variable is evaluated it returns a value dependent on the current game state.

Variable | Meaning
--- | ---
$total | Amount of players in the game.
$living | Amount of living players.
$phase | The current phase as a number.

## Sources

Sources are used internally to represent an acting active game elements. Only very few elements of formalization directly interact with sources, but it still useful to understand them. Furthermore, they are often shown or used in debug commands.

A source represents a unique named reference to an acting game element and is stored in other game elements it creates (for example polls, displays and attributes store their source).

Source consist out of two parts. A source reference (a unique reference to the acting game element) and a source name (a non-unique name for the acting game element). Each of the two parts is made up of a type and a value. For example a source reference may look like this: `player:350000308900100207` (made up of the `player` type and a unique player id), while a source name may look like this: `role:citizen` (made up of the `role` type and the player's role).

Here is a list of existing source pairs:

Reference Type | Reference Value | Name Type | Name Value | Example | Explanation
--- | --- | --- | --- | --- | ---
`player` | Member ID | `role` | Role Name | `player:350000308900100207` / `role:citizen` | Represents a player when acting through their primary role. References the player's discord id.
`player_attr` | Channel ID | `role` | Role Name | `player_attr:30050368990104207` / `role:mayor` | Represents a player when acting through a secondary/extra role. References the extra role's discord channel id.
`group` | Channel ID | `group` | Group Name | `group:45050668799134173` / `group:wolfpack` | Represents a group when acting directly. References the group's discord channel id.
`player_group` | Member ID | `group` | Group Name | `player_group:350000308900100207` / `group:wolfpack` | Represents a player when acting as the executor of a group action. References the executor's discord id.
`poll` | Poll Name | `poll` | Poll Name | `poll:election` / `poll:election` | Represents a poll. References the poll's name. 
`team` | Team Name | `team` | Team Name | `team:townsfolk` / `team:townsfolk` | Represents a team. References the team's name.
`attribute` | Attribute ID | `attribute` | Attribute Name | `attribute:13` / `attribute:wolfish` | Represents an attribute. References the attribute's unique id.

## Triggers

Triggers are one of the main parts of formalization. Triggers determine when an ability is executed. Triggers are differentiated between prompting triggers, which create a prompt for each ability (even if no input is required - in this case the player simply needs to confirm the ability) and automatic triggers. Some triggers have special selectors available within them that are not available in other contexts.

Some triggers are considered to be complex triggers. Such triggers take one or more parameters to limit their execution to certain conditions. For those triggers the expected type is listed as `{Type}`.

A list of triggers can be found here:

Trigger | Explanation | Additional Selectors
--- | --- | ---
Start Night<br>End Night<br>Start Day<br>End Day<br>Immediate Night<br>Immediate Day<br>End Phase<br>Start Phase<br>Immediate<br>Pre-End Night<br>Pre-End Day | Prompting triggers for different timings. | ⛔
Passive End Day<br>Passive End Night<br>Passive Start Day<br>Passive Start Night<br>Passive Start Phase<br>Passive End Phase | Non-prompting triggers for different timings. | ⛔
Starting | Triggers at the start of the game, and whenever a new group/role/team/attribute/etc is created. | ⛔
Passive | Triggers whenever a significant change occurs (game start, phase change, somebody dies/changes roles) and can be used with conditions that should theoretically be checked constantly. | ⛔
On Death | Triggers when the current player dies. | @Attacker<br>@DeathType<br>@AttackSource
On {Player} Death | Triggers when a player from a specific selector dies. | @Attacker<br>@DeathType<br>@AttackSource<br>@This
On Killed | Triggers when the current player is killed (true kill, kill or attack, but __not__ lynch). | @Attacker<br>@DeathType<br>@AttackSource
On {Player} Killed | Triggers when a player from a specific selector is killed. | @Attacker<br>@DeathType<br>@AttackSource<br>@This
On Banishment | Triggers when the current player is banished. | @Attacker<br>@DeathType<br>@AttackSource
On {Player} Banishment | Triggers when a player from a specific selector is banished. | @Attacker<br>@DeathType<br>@AttackSource<br>@This
On Banished | Triggers when the current player is banished?? Do not use. | @Attacker<br>@DeathType<br>@AttackSource
On {Player} Banished | Triggers when a player from a specific selector is banished?? Do not use. | @Attacker<br>@DeathType<br>@AttackSource<br>@This
On Visited<br>On Visited [{AbilityType}]<br>On Visited [{AbilitySubtype}]<br>On Visited [!{AbilityType}]<br>On Visited [!{AbilitySubtype}] | Triggers when the current player is visited. Can be filtered to limit it to or to exclude specific ability types or subtypes. | @Visitor<br>@VisitParameter<br>@VisitType<br>@VisitSubtype 
On {Player} Visited [{AbilityType}]<br>On {Player} Visited [{AbilitySubtype}]<br>On {Player} Visited [!{AbilityType}]<br>On {Player} Visited [!{AbilitySubtype}] | Triggers when a player from a specific selector is visited. Can be filtered to limit it to or to exclude specific ability types or subtypes. | @Visitor<br>@VisitParameter<br>@VisitType<br>@VisitSubtype 


 
## Game Element Formats

### Roles Format

Roles are the main holder of WWRF information. Their formalization is formatted as follows:

```
**<Role Name>** | <Role Group> <Role Category> <Role Team>
__Basics__
<Basic Description>
__Details__
<Detailed Description>

__Simplified__
<Simplified Description>

__Formalized__
[Unique Role]
[Haunted Role]
<Ability List>

__Card__
<Card Description>
```
- Role Name: May be anything  
- Role Class: May be `Townsfolk`, `Werewolf`, `Solo`, `Unaligned` or `Extra`  
- Role Category: May be `Elected`, `Align`, `Killing`, `Group`, `Investigative`, `Power` or `Miscellaneous`  
- Role Team: Should be kept blank for any role group besides `Solo`. For `Solo` should be set to `- <Team Name>`, where `<Team Name>` is the name of a defined team, e.g. `Pyro Team`.
- Unique Role: Set to `Unique Role` for unique roles, otherwise remove the line entirely
- Haunted Role: Set `Haunted Role` for roles that use ALL their ability while ghostly, otherwise remove the line entirely
- Ability List: A newline separated list of `Abilities`

### Teams Format
```
**<Team Name>**
__Basics__
<Basic Description>

__Formalized__
Win Condition: <Target List>
<Ability List>
```

- Win Condition: A comma separated list of [Player Selectors](#player-selectors). The team wins when all remaining players match one or more of the selectors.
- Ability List: A newline separated list of `Abilities`

### Groups Format
```
**<Group Name>** | <Related Team Name> Group
__Basics__
<Basic Description>
__Members__
<Members Description>

__Formalized__
[Unique Group]
<Ability List>
```

- Unique Group: Set to `Unique Group` for unique group, otherwise remove the line entirely 
- Ability List: A newline separated list of `Abilities`

### Abilities Format
(WIP)
when specifying an ability list, you may use `No Abilities` for an empty ability list
