---
title: How do I add a new cheat to the game?
---
Adding a new cheat to the <SwmToken path="src/cheat_type.h" pos="2:13:13" line-data=" * This file is part of OpenTTD." repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=" repo-name="OpenTTD">`OpenTTD`</SwmToken> game involves several steps including defining the cheat, implementing its functionality, and adding it to the user interface.

<SwmSnippet path="/src/cheat_type.h" line="26" repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=">

---

First, you need to define your new cheat in the <SwmToken path="src/cheat_type.h" pos="26:2:2" line-data="struct Cheats {" repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=" repo-name="OpenTTD">`Cheats`</SwmToken> struct in <SwmPath repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=" repo-name="OpenTTD" path="src/cheat_type.h">`(OpenTTD) src/cheat_type.h`</SwmPath>. Add a new <SwmToken path="src/cheat_type.h" pos="27:1:1" line-data="	Cheat magic_bulldozer;  ///&lt; dynamite industries, objects" repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=" repo-name="OpenTTD">`Cheat`</SwmToken> object to this struct.

```c
struct Cheats {
	Cheat magic_bulldozer;  ///< dynamite industries, objects
	Cheat switch_company;   ///< change to another company
	Cheat money;            ///< get rich or poor
	Cheat crossing_tunnels; ///< allow tunnels that cross each other
	Cheat no_jetcrash;      ///< no jet will crash on small airports anymore
	Cheat change_date;      ///< changes date ingame
	Cheat setup_prod;       ///< setup raw-material production in game
	Cheat edit_max_hl;      ///< edit the maximum heightlevel; this is a cheat because of the fact that it needs to reset NewGRF game state and doing so as a simple configuration breaks the expectation of many
	Cheat station_rating;   ///< Fix station ratings at 100%
};
```

---

</SwmSnippet>

<SwmSnippet path="/src/cheat.cpp" line="15" repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=">

---

Next, initialize your cheat in <SwmPath repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=" repo-name="OpenTTD" path="src/cheat.cpp">`(OpenTTD) src/cheat.cpp`</SwmPath>. This is where the cheats are reset to their default values.

```c++
/** All the cheats. */
Cheats _cheats;

/** Reinitialise all the cheats. */
void InitializeCheats()
{
	memset(&_cheats, 0, sizeof(Cheats));
}
```

---

</SwmSnippet>

<SwmSnippet path="/src/cheat_gui.cpp" line="200" repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=">

---

Then, add your cheat to the user interface in <SwmPath repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=" repo-name="OpenTTD" path="src/cheat_gui.cpp">`(OpenTTD) src/cheat_gui.cpp`</SwmPath>. This is where the cheats are displayed to the user and their actions are handled.

```c++
 */
static const CheatEntry _cheats_ui[] = {
	{SLE_INT32, STR_CHEAT_MONEY,           &_money_cheat_amount,                          &_cheats.money.been_used,            &ClickMoneyCheat         },
	{SLE_UINT8, STR_CHEAT_CHANGE_COMPANY,  &_local_company,                               &_cheats.switch_company.been_used,   &ClickChangeCompanyCheat },
	{SLE_BOOL,  STR_CHEAT_EXTRA_DYNAMITE,  &_cheats.magic_bulldozer.value,                &_cheats.magic_bulldozer.been_used,  nullptr                  },
	{SLE_BOOL,  STR_CHEAT_CROSSINGTUNNELS, &_cheats.crossing_tunnels.value,               &_cheats.crossing_tunnels.been_used, nullptr                  },
	{SLE_BOOL,  STR_CHEAT_NO_JETCRASH,     &_cheats.no_jetcrash.value,                    &_cheats.no_jetcrash.been_used,      nullptr                  },
	{SLE_BOOL,  STR_CHEAT_SETUP_PROD,      &_cheats.setup_prod.value,                     &_cheats.setup_prod.been_used,       &ClickSetProdCheat       },
	{SLE_BOOL,  STR_CHEAT_STATION_RATING,  &_cheats.station_rating.value,                 &_cheats.station_rating.been_used,   nullptr                  },
	{SLE_UINT8, STR_CHEAT_EDIT_MAX_HL,     &_settings_game.construction.map_height_limit, &_cheats.edit_max_hl.been_used,      &ClickChangeMaxHlCheat   },
	{SLE_INT32, STR_CHEAT_CHANGE_DATE,     &TimerGameCalendar::year,                      &_cheats.change_date.been_used,      &ClickChangeDateCheat    },
};

static_assert(CHT_NUM_CHEATS == lengthof(_cheats_ui));

/** Widget definitions of the cheat GUI. */
static constexpr NWidgetPart _nested_cheat_widgets[] = {
	NWidget(NWID_HORIZONTAL),
		NWidget(WWT_CLOSEBOX, COLOUR_GREY),
		NWidget(WWT_CAPTION, COLOUR_GREY), SetDataTip(STR_CHEATS, STR_TOOLTIP_WINDOW_TITLE_DRAG_THIS),
		NWidget(WWT_SHADEBOX, COLOUR_GREY),
```

---

</SwmSnippet>

<SwmSnippet path="/src/cheat.cpp" line="24" repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=">

---

Finally, implement the functionality of your cheat. This could be anywhere in the codebase, but <SwmPath repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=" repo-name="OpenTTD" path="src/cheat.cpp">`(OpenTTD) src/cheat.cpp`</SwmPath> is a good place to start. The <SwmToken path="src/cheat.cpp" pos="28:2:2" line-data="bool CheatHasBeenUsed()" repo-id="Z2l0aHViJTNBJTNBT3BlblRURCUzQSUzQXNodWp1dXU=" repo-name="OpenTTD">`CheatHasBeenUsed`</SwmToken> function is an example of how a cheat's functionality can be implemented.

```c++
/**
 * Return true if any cheat has been used, false otherwise
 * @return has a cheat been used?
 */
bool CheatHasBeenUsed()
{
	/* Cannot use lengthof because _cheats is of type Cheats, not Cheat */
	const Cheat *cht = (Cheat*)&_cheats;
	const Cheat *cht_last = &cht[sizeof(_cheats) / sizeof(Cheat)];

	for (; cht != cht_last; cht++) {
		if (cht->been_used) return true;
	}

	return false;
}
```

---

</SwmSnippet>

<SwmMeta version="3.0.0"><sup>Powered by [Swimm](/)</sup></SwmMeta>
