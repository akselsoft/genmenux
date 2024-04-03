# GenMenuX
GenMenuX is a menu extender for Visual FoxPro.

To install GENMENUX, specify it in your CONFIG.FP or with the System Variable
_GENMENU as in _GENMENU='GENMENUX.PRG'.  Be sure to include the full path
where you store GENMENUX.PRG.  DO NOT OVERRIDE YOUR GENMENU.PRG WITH GENMENUX.PRG

This program is based on the concept behind GENSCRNX by Ken Levy of JPL and the initial program by Steven Black.  The idea behind the "X" series of generators is that there are times when FoxPro does not provide everything required in the Power Tools.  The "X" series provides additional functionality that can add incredible power to these tools.
GENMENUX was originally conceived by Steven Black to provide automatic menu translation for his INTL GENSCRNX Driver, which creates truly international-ready applications.  Additional modifications were made to make GENMENUX a more powerful and generic tool. 
GENMENUX 3.0 supports both Visual FoxPro 3.0 and FoxPro 2.x for all platforms.
GENSCRNX was designed by Ken Levy and provides major extensions to the FoxPro screen builder in the same way that GENMENUX extends the menu builder.
Since the most common complaint I've heard about the Menu Builder is that it forces the developer to use FoxPro's format of menus : shadows, placement, etc.  GENMENUX attempts to provide power back into the developer's hands.
It enhances the FoxPro Menu Builder with the following features :
*	ability to control default menu positioning, colours and actions without manually changing the MPR file.
*	ability to remove menu pads based on logical conditions instead of using SKIP FOR.
*	ability to add Messages to DOS Menus
*	ability to automatically add hot keys to menu pads
*	ability to call menu "drivers" at various points in the Menu Generation process.
*	ability to define Menu Templates that contain standard menu objects that can be inserted at any time into an existing menu.
Technically, these generators copy a screen or menu and make modifications to the copy prior to building. This way, the original screen remains the same.  For more information on GENSCRNX and the entire concept behind the "X" series, see the documentation for GENSCRNX.
As with GENSCRNX, GENMENUX has been placed in the Public Domain.

Calling GENMENUX directives

GENMENUX directives may be called in four different locations :

CONFIG.FP/CONFIG.FPW
The CONFIG.FP is the startup configuration file that FoxPro uses. In order to use GENMENUX at all, you must specify it as the replacement for GENMENU with the following line :

_GENMENU="GENMENUX.PRG"
If you have added FoxPro into your DOS path, you should put the entire path into the GENMENUX line.

Setup// Menu Procedure Snippet

Any Setup directives may be called from either the Setup snippet or the Menu Procedure snippet. You can access the Menu Procedure snippet easier than the Setup snippet as it is immediately visible when choosing Menu options from the Menu pad.

Comment Snippet

The comment snippet is available by selecting Options for any particular menu.

Technical Note - Reordering Menus

When you create a menu using the Menu Builder,  FoxPro carefully constructs the MNX file in a particular manner.  When adding and removing menu items directly into the MNX file,  it is crucial that the order and content of the MNX file is maintained.  For example,  FoxPro maintains the number of menu items for each menu pad and bar in the MNX file.  If you remove one of the items without updating the number of items,  GENMENU builds an invalid MPR file.  

To reduce this from happening, GENMENUX reorders the menu after processing the majority of its directives and running MNXDRV1 and MNXDRV2.  This allows users to create their own drivers that add and remove menu pads and bars without worrying about messing up the order of the MNX file.  If you design such a driver, it is important that you call it in either MNXDRV1 or MNXDRV2.  If you remove menu pads in MNXDRV3 or later,  you will corrupt the MNX file and the MPR file will be unusable. 

Technical Note - Menu Templates

With the release of version 1.1, GENMENUX offers the concept of Menu Templates or Libraries, similar to the screen libraries offered with GENSCRNX.
GENMENUX Directives
(Some directives may be identified in more than one place.  At the end of each directive description, these places are identified. The directives are ordered by where they may be called.)

### GENMENUX Drivers

GENMENUX will allow for menu drivers to be called.  A menu driver is a program or procedure that is run through the temporary menu or generated MPR file making changes.  This happens at different times throughout the menu generation procedure.  To identify a driver, place the required driver directive in the Setup/Menu Procedure snippet of the menu and GENMENUX will call the procedure after it has completed its changed.
The driver specified by any of the DRV directives does not have to be highlighted in Quotes. In fact, GENMENUX will strip the quotes if there are any.  In addition, if you do not put an extension on the driver, GENMENUX will look for a driver with a PRG extension to run.
Since drivers do very similar things but at different places, there is a description at the end of this document that explains what happens when as far as drivers go.
You may call as many drivers as desired within the Setup or Procedure snippets. Within the CONFIG.FP, GENMENUX will only accept the first driver listed for a particular leve.

#### *:MNXDRV0 <expr1>
Calls a GENMENUX Driver that attaches itself to the user’s GENMENU temporarily. This allows the driver to contain modified GENMENU procedures that provide additional functionality where GENMENU does not. This driver should only contain procedure and function statements and should expect a clean (ie empty) environment when being called. 

Called : Setup/Menu Procedure snippet

#### *:MNXDRV1 <expr1> / _MNXDRV1

Calls a GENMENUX Driver before Standard GENMENUX processing has been completed.  This driver must be a complete driver in the sense that when it starts, the menu MNX file is open and at the first record.

Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:MNXDRV2 <expr1> / _MNXDRV2

Calls a GENMENUX Driver after Standard GENMENUX processing has been completed. This driver can be considered a Line driver i.e. it can simply be a one line command. It is processed through each record of the menu file from within a SCAN statement.

Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:MNXDRV3 <expr1> / _MNXDRV3

Calls a GENMENUX Driver after Standard GENMENUX processing and Menu re-ordering has been completed.  This driver can be considered a Line driver i.e. it can simply be a one line command. It is processed through each record of the menu file from within a SCAN statement.
DO NOT DELETE ANY RECORDS IN THE MNX File as your menu file will be corrupted. Use MNXDRV2 if you want to do that.

Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:MNXDRV4 <expr1> / _MNXDRV4

Calls a GENMENUX Driver before the Standard GENMENU is Called. This driver must be a complete driver in the sense that when it starts, the menu MNX file is open and at the first record.

Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:MNXDRV5 <expr1> / _MNDRV5

Calls a GENMENUX Driver that completely REPLACES the standard FoxPro GENMENU.  This driver must be a complete driver in the sense that when it starts, the menu MNX file is open and at the first record.  Whatever gets created here should be placed in the MPR file.
The MNXDRV5 driver is passed the same two parameters as the regular GENMENU.
The MNXDRV5 directive is identical to the GENMENUX directive and will override the setting of the GENMENUX driver, if used.

Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:MPRDRV1 <expr1> / _MPRDRV1
Calls a GENMENUX Driver that will update the SPR file within the temporary project file.  This driver must be a complete driver in the sense that when it starts, the just created MPR file is in the Memo field named OBJECT  A driver here might completely replace the MPR with a different program like a pre-created menu file.

Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:MPRDRV2 <expr1> / _MPRDRV2
Calls a GENMENUX Driver that will update the SPR file within the temporary project file.  This driver must be a complete driver in the sense that when it starts, the just created MPR file is in the Memo field named OBJECT  A driver here might completely replace the MPR with a different program like a pre-created menu file.
The MPRDRV2 driver is called as the very last item in GENMENUX before the removal of the temporary project files.  There is no additional GENMENUX processing except for directives that run the menu or another program after completion.

Called : Setup/Menu Procedure snippet
	CONFIG.FP

### GENMENUX Template or Library Directives
Starting with Version 1.1, GENMENUX allows a menu template or library to be created. A library allows for standard menu options to be once and placed into any menu during menu generation. For example, if every custom application you build uses a Special menu pad that contains items such as Help, About, System Information, then when you design a menu, you have to add these items in every time.  By defining the Special menu pad in a Menu Library, you can call this menu pad and it will be added to any menu automatically. A library is contained in a template file. This file is referenced by FOXMNX. Libraries have objects within them. The Special menu, mentioned above, may be in a library called STANDARD and referenced as an object called SPECIAL. This library may be created and referenced using the commands below. 

#### *:FOXMNX <cFileName> / _FOXMNX
In order for a menu library to exist, the library file must be created using the FOXMNX directive. This directive may define a library table in every application directory or it may reference a single one for different applications. The table created will have the identical structure to an MNX file with some additional fields that identify the library and objects within.

Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:DEFLIB <cLibrary>
Since every object must belong to a library, DEFLIB allows you to define the library for an entire menu. This is useful if you create a single menu that contains all of your library objects and want to place each object into a single library.

Called :	Setup/Menu Procedure snippet
	CONFIG.FP

#### *:DEFOBJ [<cLibrary>.]<cObject>
To define a library object, place the DEFOBJ directive in the comment field for the menu pad, submenu or procedure to be defined. The object will be inserted into the FOXMNX table as belonging to the library specified by cLibrary. If cLibrary is not specified, then the library will be the one identified by the DEFLIB directive.

Called :	Comment snippet

	Example of How to Define Library Objects

In the CONFIG.FP, specify the template file with the command :
_FOXMNX='FOXMNX.DBF'

This identifies the FOXMNX table as the container for any libraries.

In addition, specify the default library for any menu objects to belong to with the line :
_DEFLIB="TOOLS"

This identifies any objects in the menu as belonging to the Tools library.
On any menu pad, submenu, command or procedure that should be defined in the Library, add the DEFOBJ directive in the Comment snippet. If the About... menu pad contains a procedure that is the same between different applications, add the following line to the Comment snippet :

#### *:DEFOBJ About

This inserts a menu object into the FOXMNX table for the About menu pad, identifying it as belonging to the Tools library and naming the object About.
If the File Reindex menu pad contains a procedure that is the same between different applications but you want to place it in the File library, add the following line to the 
Comment snippet :

*:DEFOBJ FILE.REINDEX.

This inserts a menu object into the FOXMNX table for the Reindex menu pad, identifying it as belonging to the File library and naming the object Reindex.

#### Referencing Library Objects

#### *:INCLIB
To define the default library for referencing library objects, place the INCLIB directive in the Setup or Menu Procedure snippet of the menu.

#### *:INSOBJ <cLibrary>.<cObject>
To reference a library object and have it replace a menu option in a menu, place the INSOBJ directive in the comment field for the menu pad, submenu or procedure to be replaced. The object will be replaced by the object <cObject> from the FOXMNX table belonging to the library specified by cLibrary. If cLibrary is not specified, then the library will be the one identified by the INCLIB directive.

Called :	Comment snippet

### GENMENUX General Directives
#### *:AUTOACT / _AUTOACT
AUTOACT will automatically Activate the menu in the Cleanup snippet. This is useful if you use the MENUNAME directive and do not place the ACTIVATE MENU clause in your code.

Called : Setup/Menu Procedure snippet
	CONFIG.FP
First appeared : Version 1.1

#### *:AUTOHOT / _AUTOHOT <cexpr1>
This directive will automatically add hot keys to menu pads (not bars) that do not currently have one assigned. This is useful if you sometimes forget to create hot keys for your menus.  By default, it uses the first letter of the Menu Pad.  If it is already being used by one of the other menu pads, it will proceed to the next letter, and so on.
In the CONFIG.FP file, <cExpr1> can be either ON or OFF.
When adding it to the Setup/Menu Procedure snippet,  <cExpr1> is ignored.  It is automatically turned on

Called : Setup/Menu Procedure snippet
	CONFIG.FP
#### *:AUTOPOS / _AUTOPOS
AUTOPOS will allow the user to click on the Line where the menu is to start on. This results in a *:LINE directive being added to the Setup/Menu Procedure snippet.

Called : Setup/Menu Procedure snippet
	CONFIG.FP
First appeared : Version 1.1
#### *:AUTORUN / _AUTORUN
This directive will automatically run the generated MPR file once it has been generated.  This directive is ignored when building a menu within a project.

Called : Setup/Menu Procedure snippet
	CONFIG.FP
#### *:BASEHDR <cProgFile> / _BASEHDR
This directive will place the <cProgFile> into the Setup snippet of the Menu before the rest of GENMENUX is run, so you are able to load standard drivers and directives.


Called: Setup/Menu Procedure Snippet
	CONFIG.FP


First Appeared: Version 3.0
#### *:FOUNDATION <cExpr> / _FOUNDATION
FOUNDATION will place a READ VALID <cExpr> statement in your Cleanup snippet to assist in the creation of a FOUNDATION READ. If you do 	not provide a <cExpr>, the READ will automatically exit when the Prompt is either "EXIT" or "QUIT".


Called : Setup/Menu Procedure snippet
	CONFIG.FP
First appeared : Version 1.1

#### *:GENMENUX <cExpr1>

This directive allows you to define what GENMENU.PRG driver program to use.  This is helpful if you want to use a different menu generator for a particular menu.  The driver is defined in <cExpr1>.


Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:HIDE / _HIDE
HIDE will hide the menu bar during the running of the MPR file and show it at the end. This is useful if you use a lot of *:IF statements and don't want the user to see a lot of menu activity.


Called : Setup/Menu Procedure snippet
	CONFIG.FP
First appeared : Version 1.1

#### *:NOLOC / _NOLOC
NOLOC will remove the LOCFILE statement that is automatically put into the MPR by GENMENU. 


Called : Setup/Menu Procedure snippet
	CONFIG.FP
	First appeared : Version 3.0

#### *:NOXTHERM | _NOXTHERM='ON'
Tells GENMENUX to use FoxPro's standard thermometer instead of the GENMENUX Extended one. This is useful if you find the GENMENUX thermometer too distracting in your development environment.

Called : Setup/Menu Procedure snippet
	CONFIG.FP
First appeared : Version 1.1

#### *:REFPRG <cExpr> | _REFPRG <cExpr>
Identifies the program that GENMENUX will create that refreshes any clauses that may change using the *:CASE or *:ARRAY functions. The “Refresh” program can be used to refresh the menu at any time without recalling the MPR file. This makes the entire update process much faster.

Called :	Setup/Menu Procedure Snippet
	CONFIG.FP

First Appeared: Version 2.0

####  *:SELECTBAR / _SELECTBAR
Currently GENMENU places an ON BAR statement for activating popups. Using SELECTBAR will change the ON BAR statements to read ON SELECTION BAR which will change the behaviour of activating menu popups.	This works best when moving the popups in different locations on the screen.
	

Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:SELECTPAD / _SELECTPAD
Currently GENMENU places an ON PAD statement for activating popups. Using SELECTPAD will change the ON PAD statements to read ON SELECTION PAD which will change the behaviour of activating menus. This works best when moving the menus in different locations on the screen.

Called : Setup/Menu Procedure snippet
	CONFIG.FP

#### *:SYSPOP / _SYSPOP
SYSPOP is a directive that wraps the Procedure statement with PUSH MENU _MSYSMENU / SET SYSMENU OFF/POP MENU _MSYSMENU. It can be defined in either the CONFIG.FP, Setup or Procedure snippets.

Called : Setup/Menu Procedure snippet
	CONFIG.FP
	Comment Snippet
#### *:VERTICAL <nStartRow>,<nSkipRow> / _VERTICAL
VERTICAL will cause the entire menu to be created vertically instead of horizontally.  You specify the starting row with nStartRow and define how many rows are between menus options using nSkipRow.

Called : Setup/Menu Procedure snippet
	CONFIG.FP
#### *:AUTO_VER
AutoVer is a special directive that turns a menu into a Version number generating tool that is updated every time a menu is rebuilt.  In order to use the AUTO_VER directive, a menu must only have one pad and that pad should be a Procedure. The Prompt will contain the current version number and the Procedure will contain the code that should occur when the user runs the MPR file.
For example, if you want to set a global variable called GCVERSION that holds the latest version number, the following code should be in the Procedure statement:
gcVersion=[1.00.<<PADL(prompt,3,'0')>>]
Note that the Prompt statement is put in << >>. This is because the Procedure statement is evaluated as a Text Merge program. 
AUTO_VER is also special in that if there is any code in the Comment snippet, it will be evaluated before updating the prompt. Here is where additional code may be run to create version logs or perform other tasks. If the internal variable _UpdateVersion is set to False after the Comment snippet has been evaluated, then the Prompt is not incremented by one.
Many thanks to Lisa Slater and Matt Peirse for their initial work on this type of functionality.

First Appeared: Version 3.0
#### *:DEFCOMMAND_[ALWAYS|INCLUDE] <cCommand>
DEFCOMMAND is a directive that allows you to predefine global commands for any menu prompts that do not have commands written for them. In this respect it is similar to the Menu Procedure, however, DEFCOMMAND only works with commands and updates them based on the ALWAYS or INCLUDE flag. The ALWAYS flag will always replace the command statement, even if it’s already filled in. The INCLUDE flag will only replace command statements that are empty.
Example: *:DEFCOMMAND_ALWAYS Do ChkComm WITH Prompt()
	*:DEFCOMMAND_INCLUDE DO chkComm WITH PROMPT()

First Appeared: Version 3.0 
#### *:INSCX <cWinName>  [SAVE [MODAL]]
INSCX will place a DO MENU.MPR in the SCX specified by cWinName. If there are no clauses, INSCX will place the statement at the bottom of the Screen Setup Code. If SAVE is specified, INSCX will place a PUSH MENU and POPMENU in the Setup and Cleanup code. If MODAL is also specified, INSCX will place the DO command in the When Clause for the screen and the POP MENU _MSYSMENU clause in the Deactivate snippet.

Called : Setup/Menu Procedure snippet
First appeared : Version 1.1
#### *:LINE <val1>
Allows the Menu bar to start at the Row defined by val1.  This can be used to force menus to start at a particular row instead of the default Row 0.

Called : Setup/Menu Procedure snippet
#### *:LOCATION <cExpr>,<cExpr2>
LOCATION will update the location of the menu based on <cExpr>. cExpr can either be "REPLACE","APPEND","BEFORE", or "AFTER" (just like the Menu options.) If cExpr is BEFORE or AFTER, cExpr2 should contain either the system menu prompt or pad name that the menu is before. 

Called : Setup/Menu Procedure snippet
First appeared : Version 1.1
#### *:MENUCOLOR <cExpr>
Adds the COLOR statement to the DEFINE MENU clause of the menu. This will only work if the menu has been renamed or is being moved to a different position.

Called: Setup/Menu Procedure snippet

First Appeared: Version 2.0
#### *:MENUNAME <expr1>
Renames the Menu bar from _MSYSMENU to expr1.  <expr1> does not have to be quotes as GENMENUX will strip any quote delimiters from expr1..

Called : Setup/Menu Procedure snippet
#### *:NOACT
This directive removes the ACTIVATE MENU _MSYSMENU statement from the generated MPR file.

Called : Setup/Menu Procedure snippet
First Appeared : Version 1.1a
#### *:NOAUTO
This directive removes the SET SYSMENU AUTOMATIC statement from the generated MPR file.

Called : Setup/Menu Procedure snippet
#### *:NOBAR
Removes the BAR setting from MPR file allowing for menus to be created that only are the length of the menu bar itself instead of extending across the screen.

Called : Setup/Menu Procedure snippet
#### *:NOGEN
Directs GENMENUX to not generate the MPR file.

Called : Setup/Menu Procedure snippet
#### *:NOMARGIN
Removes MARGIN setting from MPR file allowing for menus to be created as shown.

Called : Setup/Menu Procedure snippet
#### *:NOSHADOW
Removes SHADOW setting from MPR file allowing for menus to be created as shown.

Called : Setup/Menu Procedure snippet
#### *:NOXGEN
Directs GENMENUX to ignore any GENMENUX directives and generate the MPR file as though GENMENUX were not present.

Called : Setup/Menu Procedure snippet
#### *:PADCOLOR <cExpr1>
Changes the default Menu PAD Color setting.  Normally, GENMENU defaults to COLOR SCHEME 4.  Using PADCOLOR, the default scheme can be changed to any particular setting.

Called : Setup/Menu Procedure snippet
#### *:POPCOLOR <cExpr1>
Changes the default Menu Popup Color setting.  Normally, GENMENU defaults to COLOR SCHEME 3.  Using POPCOLOR, the default scheme can be changed to any particular setting.

Called : Setup/Menu Procedure snippet
#### *:SKIP_AUTO
Tells GENMENUX to put a “DO <cSkipProg>“ in the Cleanup code so it can run the SKIP_REDIRECT program automatically.

Called: Setup/Menu Procedure Snippet
#### *:SKIP_REDIRECT <cSkipProg>
Moves all of the SKIP statements in the menu into a program specified by <cSkipProg>. This allows the calling of this program at any time in the program without having to rerun an MPR file.

Called : Setup/Menu Procedure snippet
#### *:SYSDEFAULT
Adds the statement "SET SYSMENU SAVE" to the Clean up code.  This makes the menu become the default (SET SYSMENU TO DEFAULT) after it has been created.

Called : Setup/Menu Procedure snippet
#### *:WINDOW <cExpr1> [CLAUSES <cExpr2>]
Adds the line IN WINDOW <expr1> command to the MPR file.  This allows you to create menus within your own user-defined windows.  When using WINDOW, be sure to DEFINE the window before running the MPR file. If not,  the MPR file will simply use the regular FoxPro desktop to place the menu.
If you use the CLAUSES command, WINDOW will automatically create a window named <cExpr1>. You may specify additional clauses for the DEFINE WINDOW statement in <cExpr2>. If you do not specify a FROM clause, WINDOW will allow you to Size and Place it during compilation by displaying a Window on the screen.

*:WINDOW works well under DOS to create MDI-style applications.  You can simply add *:WINDOW to your setup code and it will work fine.

Example : *:WINDOW w_temp CLAUSES FROM 5,5 TO 12,50 COLOR SCHEME 6

Called : Setup/Menu Procedure snippet
#### *:XPLATKEYS
Sets up the menu to provide support for multiplatform hotkeys that may be different. This tells GENMENUX to support the directives *:KEYWIN, *:KEYMAC, *:KEYDOS and *:KEYUNIX to hold onto different key labels for the code.
See section on *:KEY

Called: Setup/Menu Procedure snippet


First Appeared: Version 3.0
#### *:BARHOT
BARHOT will create hot keys for all menu bar items in the same way AUTOHOT creates hot keys for Menu Pads. This directive can only be called in the Comment snippet. However, if it is called at the top pad of a menu, all of the items in the submenu will be updated accordingly.

Called : Setup/Menu Procedure Snippet
	Comment snippet
First appeared : Version 1.1


#### *:AFTER <cExpr>
AFTER will place the AFTER clause on the DEFINE BAR statement for the specific menu bar. This allows you to identify the location of the menu bar in case you are removing or adding various menu bars with other GENMENUX directives.

Called:	Comment Snippet

First Appeared: Version 2.0
#### *:ARRAY <cArray>
Adds a loop for a popup that allows the BARS of the popup to be defined based on the array <cArray>. If the Array is two dimensional, the ON SELECT BAR statement calls the command identified in the second column of the array.

Called : Comment snippet
Example : *:ARRAY a_fldNames
First appeared : Version 1.1

#### *:BEFORE <cExpr>
BEFORE will place the BEFORE clause on the DEFINE BAR statement for the specific menu bar. This allows you to identify the location of the menu bar in case you are removing or adding various menu bars with other GENMENUX directives.

Called:	Comment Snippet

First Appeared: Version 2.0
#### *:CASE <cExpr>
This directive allows menu items to appear based on the condition defined in <cexpr1>.  This condition is evaluated at runtime, allowing menu items to completely disappear when not being used (as opposed to being greyed out).  What it does is add a CASE statement in the Cleanup code that RELEASEs menu pads that do not meet the condition specified. 
CASE is useful for menu conditions that are repeated throughout the MPR file such as platform specific options (*:CASE _DOS, *:CASE _WINDOWS) or developer options (*:CASE glDeveloper), etc.
The CASE directive does not support the IF Keywords.

Called : Comment snippet
Example : *:CASE _DOS
First appeared : Version 2.0


#### *:CLAUSE <cClauses>
CLAUSE will add any additional clauses that are needed to the BAR or PAD definition. This allows users to identify any new items that are added to the DEFINE BAR statement.

Called : Comment snippet

First Appeared: Version 3.0
First appeared : Version 1.1

#### *:COLOR <cColorPair>
COLOR allows the definition of an entire Color Pair setting to an individual menu pad or item. This allows individual menu items to use different color pairs.
Under Windows, COLOR also makes it easy to identify colors for entire menu bars with several key colors. These color settings are then translated into their appropriate RGB settings. These colors are:
Red
Green
White
Grey
Maroon
Aqua
Royal Blue
Burgundy
Light Green
Khaki
Baby Blue
Blue
Black
Violet
Yellow
Dark Grey


Called : Comment snippet
Example : *:COLOR R/W,RB/B
		*:COLOR Red
First appeared : Version 1.1

#### *:COLORSET <cExpr>
ColorSET allows you to define a color scheme for an individual menu pad or item. This allows individual items to use different schemes.

Called : Comment snippet
Example : *:COLORSET 7
First appeared : Version 1.1
	
#### *:DEFPOPIF <cExpr>
DEFPOPIF will make the actual DEFINE POPUP statement conditional on whether or not it already exists. The DEFINE POPUP statement is rather slow so this directive will place an IF...ENDIF statement around the popup definition for cExpr so the definition is conditional, making the MPR run faster.

Called : Comment snippet
Example : *:POPDEFIF options
First appeared : Version 1.1

#### *:DELETE
Deletes menu prompt during compilation so it doesn't appear under menu. This directive is useful for adding features at a later date and ensuring they don't appear in the menu.

Called : Comment snippet
#### *:DELOBJ
DELOBJ will remove a menu item after GENMENUX has completed directive processing but BEFORE it reorders the menu.

Called : Comment snippet
First appeared : Version 1.1
#### *:FONT <cFontName> [csize...]
FONT will allow you to specify a font name for a particular menu pad. You can add additional clauses for fonts after this clause as well.
The FONT Directive only works with Visual FoxPro 3.0.
Example: *:FONT “Arial”,12
	   *:FONT “Arial”,10 STYLE “B”

Called : Comment snippet
		First appeared : Version 3.0
#### *:GENIF <cexpr1>
This directive allows menu items to be defined during MPR generation based on the condition defined in <cexpr1>.	If the condition specified in cExpr1 is not met when the MPR file is being generated,  GENMENUX will remove the PAD or BAR from the menu.

Called : Comment snippet
Example :
	*:GENIF "DEV"$CURDIR()
	*:GENIF DATE()<CTOD("12/31/93")
#### *:IF <cexpr1>
This directive allows menu items to appear based on the condition defined in <cexpr1>.  This condition is evaluated at runtime, allowing menu items to completely disappear when not being used (as opposed to being greyed out).  What it does is add an IF statement in the Cleanup code that RELEASEs menu pads that do not meet the condition specified.  
The *:IF statement brackets the cExpr in the cleanup code.
The IF directive also supports Keywords that allow you to use the values of the Bar Number, Prompt or popup name. These keywords must be wrapped in quotes and stars in order to work properly. 
The valid keywords are :
	*PROMPT*	- Returns Prompt of Menu Object
	*ITEMNUM*	- Returns Item Number of Menu Object
	*LEVEL*	- Returns Level Name of Menu Object
	*NAME*	- Returns Pad Name of Menu Object

Called : Comment snippet
Example :
	*:IF sec_level='*'
	*:IF FILE("CLIENTS.APP")
	*:IF chk_sec("*PROMPT*","*ITEMNUM*")
#### *:IGNORE
Ignore any GENMENUX directives for this menu prompt.

Called : Comment snippet
#### *:KEY<<platform>  <cKeyLabel>
The directives *:KEYWIN, *:KEYUNIX, *:KEYMAC, *:KEYDOS allow the menu to define separate key labels for each platform.

Called: Comment snippet

First Appeared: Version 3.0
*:KEYLAB <cLabel>
This directive updates the Key Label setting to whatever item is identified in cLabel. cLabel can be a variable and therefore hold onto additional settings if needed.

Called: Comment snippet

First Appeared: Version 3.0
#### *:MESSAGE <cexpr1>
Adds a message prompt as defined by <cexpr1>.  <cExpr1> may be a function or string. If it is a string, it should be enclosed in Quotes.  In FoxPro for Windows, *:MESSAGE overrides what is in the Message clause.

Called : Comment snippet
#### *:PADNAME <cExpr>
Ensures that the current pad's name is <cExpr>. 

Called : Comment snippet
Example : *:PADNAME options
First appeared : Version 1.1
	
#### *:PADPOS | POPPOS <nRow>,<nCol>
PADPOS and POPPOS will place the PAD or Popup at the Row and Column specified by nRow and nCol. This is useful for moving your menu around the screen in different locations for your various popups. POPPOS must occur within a Submenu.

Called : Comment snippet
Example : *:PADPOS 5,6	(This will position the pad at row 5, column 6)
First appeared : Version 1.1
#### *:POPCOMMAND <cExpr1>
This directive identifies the action taken when a file has been chosen from a popup created with POPFILES or POPFIELDS.  <cExpr1> must be a valid FoxPro function or command.
Example : *:POPCOMMAND DO deaPop WITH PROMPT()
	*:POPCOMMAND WAIT WINDOW "You chose "+PROMPT()

Called : Comment snippet
#### *:POPFIELD <cExpr1>
This directive allows you to create menus that offer popups of specific fields in the current table.  The popup will show the field <cExpr1> for all records from the current table.  In order to have something happen when a file is selected from the popup, you must use the directive *:POPCOMMAND.
Example : *:POPFIELD name
	*:POPFIELD "Client Name : "+name

Called : Comment snippet

#### *:POPFILES <cExpr1>
This directive allows you to create menus that offer popups of specific files. The popup will show all files matching the skeleton in <cExpr1>.  In order to have something happen when a file is selected from the popup, you must use the directive *:POPCOMMAND.  Place this directive in the Comment snippet of the first menu bar where you want the popup to be.  For example, if you want a popup to appear under the File Open menu, add a submenu to the Open option and the first bar, add the POPFILES directive to the comment snippet.
Example : *:POPFILES *.PJX
	*:POPFILES "*.DBF"

Called : Comment snippet
#### *:POPNAME <cExpr>
Ensures that the current popup's name is <cExpr>. If POPNAME is called on a System Level Pad, it changes the popup of the menu below it.

Called : Comment snippet
Example : *:POPNAME Options
First appeared : Version 1.1
	
#### *:POPPRECOMMAND <cExpr>
Allows for a command or function to be called BEFORE a Popup is defined. This currently only works when using POPFILES or POPFIELD. This allows you to open up a table to use the POPFIELD directive with more easily. You are responsible for closing any opened tables.

Called : Comment snippet
Example : *:POPPRECOMMAND =OPENFILE("CLIENTS")
First appeared : Version 1.1
	
#### *:POPTITLE <cExpr>
POPTITLE will define the popup in which the directive is called with a title of <cExpr>. This is kind of handy if you want to have titles on any of your popups.

Called : Comment snippet
Example : *:POPTITLE "Menu Options"
First appeared : Version 1.1
#### *:PREDEF <cExpr>
PREDEF will place the <cExpr> clause one line before the DEFINE BAR or DEFINE PAD statement is called. This allows you to place variables into the prompts of the code.
The PREDEF statement is evaluated twice: once right after the first MNX driver is called and once right before GENMENU is called. This allows menu libraries to also use the PREDEF command.

Called : Comment snippet
Example : *:PREDEF lcKey = GETKEY (“Hello”)
would result in the statement appearing before the current item is defined in the MPR.
First appeared : Version 3.0

#### *:TRNTXT <cExpr>||<cExpr2>
Replaces all occurrences of <cExpr> with <cExpr2> in the procedure snippet.	

Called : Comment snippet
Example : *:TRNTXT "Client"||"Bad Guys"
First appeared : Version 1.1
{{}} 
Text surrounded by double braces performs the EVALUATION of <expC> at compile time and returns the value in string form.  {{<expC>}} is replaced with the string of EVALUATE(<expC>).  <expC> can be any type (character, numeric, date, logical, etc.) and {{<expC>}} will always return the result in character form.
Example:
If the following command was in the Setup/Menu Procedure snippet and assuming the current date was 06/01/93:
WAIT 'Version Date is {{DATE()}}' WINDOW NOWAIT
the following code would be placed in the .MPR:
WAIT 'Version Date is 06/01/93' WINDOW NOWAIT
{{&.<expC>}}
Text surrounded by double braces with a &. immediately after the open braces performs the macro substitution of <expC> at compile time and returns a null value in string form.  {{<expC>}} is replaced with a null string.  <expC> can be any FoxPro command that can be executed within a macro substitution string.
Example:
If the following command was in the Setup/Menu Procedure snippet and assuming the current date was 06/01/93:
{{& WAIT '{{DATE()}}' WINDOW NOWAIT}}
the following WAIT window would appear at compile time of the screen:
06/01/93
If the following command was in the Setup/Menu Procedure snippet:
{{&.DO MYPROG}}
then a program called MYPROG would be executed as a subroutine at compile time of the screen.  If the program was to return a character string for code insertion, then {{MYPROG()}} would have been used.

Called : Anywhere

Notes for FoxPro Windows
FoxPro for Windows handles Menus slightly differently than DOS in order to conform to the Windows standard.  One of the differences is that the main system menu _MSYSMENU is not allowed to be moved from the top of the desktop.  To overcome this, add the directive *:MENUNAME to your menu and rename it to something other than _MSYSMENU.  This will result in a Menu that will use the default FoxPro for Windows font which is FoxFont Size 10.  If you want to have a more "Windows" like menu,  DEFINE your window using the FONT clause of "MS Sans Serif",10.

 
Order in Which GENMENUX Handles Drivers and Directives
This gives an idea of when specific drivers are called so you can better determine where to call a driver.
1.	Definition of Variables
2.	Creation of Temporary Files.
3.	Verify NOXGEN Directive.
	Place GENMENUX Header in Setup code
	If SYSDEFAULT directive is used, place in clean up snippet.
	Identify Variables for processing other setup directives.
	Identify new menu name.
4.	Run MNXDRV1 on entire MNX file.
5.	Update menu Hot keys by scanning through each pad line by line.
6.	Process the following directives line by line.
	IGNORE
	GENIF
	DELETE
	MESSAGE
7.	Run MNXDRV2 on each line of the MNX file.
8.	Reorder Menu pads that may have been deleted because of previous directives.
9.	Generate IF support by adding IF statement to CLEANUP code.
10.	Run MNXDRV3 on each line of the MNX file.
11.	Run MNXDRV4 on entire MNX file.
12.	Identify MNXDRV5 driver. If it doesn't exist, check for _GENMENUX directive.
	Also identify future MPR drivers so we don't have to go back into the file later on.
13.	Run either GENMENU or MNXDRV5.
14.	Copy generated MPR file into Memo file of temporary project.
15.	Compare MNX and MPR file line by line for adding of POPFILES, POPFIELDS and POPCOMMAND directives.
16.	Run MPRDRV1.
17.	Process directives through MPR file.
	NOMARGIN
	NOBAR
	WINDOW
	POPCOLOR
	PADCOLOR
	NOSHADOW
	LINE
	MENUNAME
	{{}}
18.	Run MPRDRV2.
19.	Remove Temporary files.
20.	If AUTORUN, run MPR file.


COPYRIGHT NOTICE
Compressed file: GENMENUX.ZIP
System: GenMenuX
Author: Andrew Ross MacNeill
Copyright: None (Public Domain)
All source code and documentation contained in GENMENUX.ZIP has been placed into the public domain.  You may use, modify, copy, distribute, and demonstrate any source code, example programs, or documentation contained in GENMENUX.ZIP freely without copyright protection.  All files contained in GENMENUX.ZIP are provided 'as is' without warranty of any kind.  In no event shall its authors, contributors, or distributors be liable for any damages.
COMMENTS/SUGGESTIONS/PROBLEMS/QUESTIONS
Please use CompuServe's FoxForum (section 3rd Party Products) directed to:
Andrew Ross MacNeill 76100,2725

 
GENMENUX Quick Directive List

CONFIG.FP Directives - all CONFIG.FP directives are preceded with a _
	
AUTOACT	Automatically Activates the menu after creating it.
AUTOHOT	Automatically adds Hot keys to top menu bar without labels. (ALT+ only)
AUTOPOS	Allows the developer to place the menu on a line when it is being compiled.
AUTORUN	Automatically runs the menu upon completion.
BASEHDR	Identifies a standard MNX header file.
FOUNDATION	Automatically creates a FOUNDATION READ at the bottom of the menu file.
FOXMNX	Defines the Menu Template File.
GENMENUX	Runs driver specified instead of standard GENMENU.
HIDE	Hides the Menu while the MPR file is being run so menu pads won't distract the user.
MNXDRV1-5	Identifies Driver x for the MNX file
MPRDRV1-2	Identifies MPR drive for the MPR object file
NOXTHERM	Removes the GENMENUX Extended Thermometer in favour of the traditional FoxPro.
REFPRG	Identifies Refresh Program that GENMENUX will create to help in the refreshing of conditional CASE and ARRAY menus.
SYSPOP	Wraps all procedures with PUSH MENU _MSYSMENU ,POP MENU _MSYSMENU statements to preserve menu settings before a procedure is called.
	
#### Setup/Menu Procedure Directives

All setup directives are preceded with a *:
* {{}}	Identical functionality to {{}} in GENSCRNX.
* AUTOACT	Automatically Activates the menu after creating it.
* AUTOHOT	Automatically adds Hot keys to top menu bar without labels. (ALT+ only)
* AUTOPOS	Allows the developer to place the menu on a line when it is being compiled.
* AUTORUN	Automatically runs the menu upon completion.
* AUTO_VER	Sets up menu to be a version control menu
* BARHOT	Automatically adds CTRL hot keys for a menu item or submenu.
* DEFCOMMAND_ALWAYS
* DEFCOMMAND_INCLUDE	Replaces any code for Command bars with specified command. ALWAYS replaces every command bar. INCLUDE only updates any empty ones.
* DEFLIB	Defines the Default Library to be used when defining menu template objects.
* FOUNDATION	Automatically creates a FOUNDATION READ at the bottom of the menu file.
* FOXMNX	Defines the Menu Template File.
* HIDE	Hides the Menu while the MPR file is being run so menu pads won't distract the user.
* INCLIB	Defines the Default Library to be used when retrieving menu template objects.
* INSSCX	Inserts a DO .MPR call within a screen file. Also allows you to call the menu from within a MODAL screen and preserve the original menu settings.
* LINE	Places the menu at line specified. This should be a numeric value. Be warned under Windows!
* LOCATION	Allows you to set the location of the menu in relation to the current menu (REPLACE, APPEND, BEFORE or AFTER) instead of having to identify it under Menu Options.
* MENUCOLOR	Adds the COLOR clause setting to the DEFINE MENU statement if one is present in the MPR file.
* MENUNAME	Renames the default _MSYSMENU menu name in the MPR file to whatever is specified. This does not have to be in quotes. If it is, the quotes are stripped from the word.
* MNXDRV0-5	Identifies Driver x for the MNX file
* MPRDRV1-2	Identifies MPR drive for the MPR object file
* NOACT	Removes the statement ACTIVATE MENU _MSYSMENU from the MPR file.
* NOAUTO	Removes the statement SET SYSMENU AUTOMATIC from the MPR file.
* NOBAR	Removes the BAR statement. Usually only required if renaming menu. Be warned under Windows!
* NOGEN	Does not generate the MPR file
* NOLOC	Removes the LOCFILE statement from the MPR file.
* NOMARGIN	Removes the MARGIN clause from the MPR file.
* NOSHADOW	Removes the SHADOW clause from the MPR file.
* NOXGEN	Does not process any GENMENUX directives.
* NOXTHERM	Removes the GENMENUX Extended Thermometer in favour of the traditional FoxPro.
* PADCOLOR	Changes the Default Menu Pad color from color Scheme 4 to whatever number is specified. No quotes please!
* POPCOLOR	Changes the Default Menu Popup color from color Scheme 4 to whatever number is specified. No quotes please!
* REFPRG	Identifies Refresh Program that GENMENUX will create to help in the refreshing of conditional CASE and ARRAY menus.
* SELECTBAR	Changes the ON BAR statements to ON SELECTION BAR.
* SELECTPAD	Changes the ON PAD statements to ON SELECTION PAD.
* SKIP_AUTO	Automatically calls the program specified by *:SKIP_REDIRECT in the Cleanup snippet of the menu.
* SKIP_REDIRECT	Moves all of the SKIP statements into the specified program.
* SYSDEFAULT	Makes the Menu become the FoxPro default by adding the line SET SYSMENU NOSAVE to the bottom of the MPR file.
* SYSPOP	Wraps all procedures with PUSH MENU _MSYSMENU ,POP MENU _MSYSMENU statements to preserve menu settings before a procedure is called.
* VERTICAL	Makes a menu orientation vertical instead of horizontal starting at a particular location.
* WINDOW	Allows you to place a menu within a window and define the window within the MPR with specific window definition clauses.


#### Comment Directives
All Comment directives are preceded with a *:
	
* {{}}	Identical functionality to {{}} in GENSCRNX.
* ARRAY	Makes a menu popup definable by an array instead of within the MPR file. Good for dynamic menus.
* BARHOT	Automatically adds CTRL hot keys for a menu item or submenu.
* CASE	Adds or removes a menu bar when a general condition is not met.
* CLAUSE	Identifies any additional settings for a DEFINE BAR statement.
* COLOR	Allows you to designate a Color Pair setting for a particular menu item. Under Windows and Macintosh, allows you to identify color by name.
* COLORSET	Allows a specific color scheme setting for a particular menu item.
* DEFOBJ	Defines the current menu pad/procedure as a menu object in the menu template file.
* DEFPOPIF	Makes the definition of a menu popup conditional if the menu popup already exists. This has no effect on the options of the popup but will speed up the running of the MPR file.
* DELETE	Removes menu pad prior to calling GENMENU
* DELOBJ	Removes menu pad prior to calling GENMENU but AFTER initial GENMENUX directives has processed.
* FONT	Identifies a FONT setting for the menu bar.
* GENIF	Removes a menu pad or bar from the MNX file during compilation if condition is not met.
* IF	RELEASES a menu PAD or BAR if condition is not met
* IGNORE	Tells GENMENUX to ignore the menu or the pad for GENMENUX processing.
* INSOBJ	Inserts a menu object from a menu template file.
* KEYLAB	Identifies a variable to replace the KEY LABEL setting.
* MESSAGE	Adds a message clause to the menu definition. This replaces the FPW's message clause. The message must be enclosed in quotes unless it is a variable.
* PADNAME	Identifies a particular name to a menu pad without having to go into Menu Options.
* PADPOS	Defines the menu pad at a particular row and column.
* POPCOMMAND	Adds the line ON SELECTION POPUP for each popup using POPFILES or POPFIELD, the expression must be a valid FoxPro command. Quotes ARE NOT STRIPPED from the expression.
* POPFIELD	Allows for a popup of fields matching the file spec matching the expression. Quotes are not needed and if used, will return the text instead of the field.
* POPFILES	Allows for a popup of files matching the file spec matching the expression. Quotes are ignored in the expression as per standard FoxPro behaviour
* POPNAME	Identifies a particular name to a menu popup without having to go into Menu Options.
* POPPOS	Defines the menu popup at a particular row and column.
* POPPRECOMMAND	Identifies a particular command to be run before a popup is defined. For use with the POPFIELD and POPFILES directives only.
* POPTITLE	Identifies a particular title for a menu popup.
* PREDEF	Places a command BEFORE the DEFINE BAR statement occurs.
* SYSPOP	Wraps all procedures with PUSH MENU _MSYSMENU ,POP MENU _MSYSMENU statements to preserve menu settings before a procedure is called.
* TRNTXT	Translates Text within a procedure file.

