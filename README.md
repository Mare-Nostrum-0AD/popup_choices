# Script Popup Choices for 0 A.D.

![Example](https://github.com/Mare-Nostrum-0AD/popup_choices/blob/main/.github/preview.png?raw=true)

This little mod allows you to create popup choices in scenario scripts. Each popup choice menu can have up to three buttons, with each one optionally triggering a script function.

Popup choices are called via the GuiInterface component, with the function "PushPopupRequest". PushPopupRequest takes a single argument, consisting of the following fields:

- players: an array of player ids of the player(s) who should receive the popup choice
- title: the title of the popup menu
- text: explanatory text to be displayed in the middle of the popup menu
- choices: an array of two-element arrays, each one representing a button in the popup menu. Each choice consists of two elements in the following order:
	- the caption to display on the button
	- the instructions for the callback function to be triggered when the button is pressed (or null, if you want no callback function)

The instructions for callback functions are objects with the following fields:

- entities: an array of entities on which to call the function
- iid: the interface id of the component to use (i.e. IID\_Trigger, IID\_Health)
- func: the name of the function to call in the component (i.e. "Reduce" if the component is Health)
- args: an array of arguments to feed to the function

The following is an example contained in the demo scenario "DEMO: Popup Choices" (see maps/scenarios/demo\_popup\_choices\_triggers.js):
```javascript
Trigger.prototype.RequestChoiceFreeUnits = function()
{
	const cmpGuiInterface = Engine.QueryInterface(SYSTEM_ENTITY, IID_GuiInterface);
	const cmpPlayerManager = Engine.QueryInterface(SYSTEM_ENTITY, IID_PlayerManager);
	const humanPlayers = cmpPlayerManager.GetNonGaiaPlayers().filter(id => !QueryPlayerIDInterface(id).IsAI());
	for (const playerID of humanPlayers)
	{
		const choiceFreeInfantry = {
			"entities":	[SYSTEM_ENTITY],
			"iid":		IID_Trigger,
			"func":		"ChoiceFreeUnits",
			"args":		[playerID, ["Infantry"]]
		};
		const choiceFreeCavalry = {
			"entities":	[SYSTEM_ENTITY],
			"iid":		IID_Trigger,
			"func":		"ChoiceFreeUnits",
			"args":		[playerID, ["Cavalry"]]
		};
		cmpGuiInterface.PushPopupRequest({
			"players":		[playerID],
			"title": 		"Choice: Free Units",
			"text":			"Would you like to train free infantry or free cavalry for this match?",
			"choices":		[
				["Infantry", choiceFreeInfantry],
				["Cavalry", choiceFreeCavalry]
			]
		});
	}
};
```
