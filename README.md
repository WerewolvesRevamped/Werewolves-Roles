# Werewolves-Roles
The role book for Werewolves Revamped.  

# Public Information 
Please add all new roles in the "limited" folder, as all roles we add usually go through a testing process first before being fully added. 
Please make sure that your has a name that won't be confused with an existing role and please specify the category of the role. Look at other roles for reference. 
Only Solo roles also specify their team (if they have one).
In a role's description the name of the role itself is capitalized, but other role names are not.

# Game Master Information
## Pull Requests
All pull requests should be reviewed & approved by an Admin that didn't make it. Grammar changes & similiar can be commited without a Pull Requests, but please use Pull Requests for any actual changes.  

## Auto Role Sync
Anything within the "elected", "limited", "categories", "archive" folders is considered a role. Anything in subfolders of "werewolves", "townsfolk", "other" and "unaligned" is also considered a role.    Roles are sorted into categories according to what folder they're in. Each category has a header in "\_categories". 
The "channels" folder is responsible for channel commands and info.

## Book Generation
The "book" file determines in which order the categories are displayed. Within a category elements are displayed alphabetically.  

For categories that don't have subfolders the name of the folder has to be used (e.g. "limited").  
Categories with subfolders should be opened with the name of the primary folder (e.g. "townsfolk").  
Both of these use a numbering system that's increased by one every time one of these is used (e.g. 1. Limited, 2. Townsfolk)  
This is then followed by both the category and subfolder (e.g. "townsfolk/miscellaneous") or if it's unique just the subfolder (e.g. "/hell"). These use their own internal numbering system (e.g. 1.1 Townsfolk Miscellaneous, 1.2 Hell, etc).  

The path of the category is automatically used as the name of the category ("limited" => "Limited", "townsfolk/miscellaneous" => "Townsfolk Miscellaneous", "/hell" => "Hell").  
The path of the category is automatically used to display a category header ("limited" => "limited", "townsfolk/miscellaneous" => "townsfolk miscellaneous", "/hell" => "hell") from the "\_categories" folder. This has to exist, but can be empty.  

Contents of the "\_rules" folder can be referenced just like roles. They will also be added to the bot as a role, though new rules will require aliases to be usable. The top line of rules is automatically updated to a name based on the file name.

Empty lines will be ignored entirely.  

## How To Play Generation
You can write whatever text you want. If a line begins with `@` it will be considered a reference to another file. The syntax is `@x path` where x is the number of lines that should be skipped in that file (when that file has a heading that you want to skip), and path is the path to the file. E.g. `@0 _categories/how to play`. Lines starting with `>` are treated like lines beginning with `@`, however `> ` is prepended to each line of the referenced file.
