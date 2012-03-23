Special optional card attribute: "alternate" 
This property is designed to accept the guid of another card. 

Special optional card attribute: "dependent" *** Functionality not yet implemented; Subject to change.
This parameter is designed to accept either a boolean value (True/False) or the guid of another card. Exclusively used in the Deck Editor.

Python Changes:

2 new functions

isAlternate(int CardID)
SwitchWithAlternate(int CardID)

The default behavior of isAlternate(int id) is to return false.
The default behavior of SwitchWithAlternate(int id) is to do nothing.

In order to take advantage of this new functionality, a set definer must add a new attribute to each card for which he/she would like to enable these functions. The attribute must be named "Alternate", and its value must be the GUID of another card in the set. If these conditions are met, calling SwitchWithAlternate will effectively switch the current card with the predefined alternate version. The card's picture will change, and any custom card properties will be replaced with the values for the corresponding alternate version. After calling this function, isAlternate will return true.

As mentioned previously, calling SwitchWithAlternate() on a card without the "Alternate" property defined will do nothing, with the exception of a card that is the end of an alternate chain. An alternate chain may develop if card A has card B as its alternate, and card B has card C as its alternate (and perhaps so on and so forth). Calling switchWithAlternate() on A will turn it into B, and calling it again will turn it into C. In this case, isAlternate will remain true until the original card is switched back in.

Let me illustrate how this works.

 If Card A has Alternate = Meryl Streep, and card Betty White has Alternate = Sandra Bullock, And Sandra Bullock has Alternate = Kevin Bacon, and Kevin Bacon's Alternate = Canadian Bacon, and Canadian Bacon's alternate is undefined, Then calling SwitchWithAlternate will function as follows:

Number of SwitchWithAlternate calls			isAlternate()
0	Card A			Betty White		false
1	Meryl Streep		Sandra Bullock		true
2	Kevin Bacon		Kevin Bacon		true
3	Canadian Bacon		Canadian Bacon		true
4	Card A			Betty White		false

As illustrated, multiple cards can have the same card identified as their alternate versions. OCTGN will return a card to its original state upon switching from a card whose alternate is undefined. In the above example, Canadian Bacon's alternate is undefined. This means it's the end of the chain.

This affect persists across all card groups, including the table. It is up to the game definer to decide the how best to use this functionality in the game. To ease with the logic required by the scope of possibilities, there are some quality of life improvements:

Special Card Attribute: dependent (Subject to change: Not yet fully implemented.)

If this property is undefined, set to the empty string, or set to some case-insensitive variant of the string "false", adding the card to a decklist from within the deck editor will behave as normal.

If this property is set to any other value, the deck editor will ensure that all attempts to add this card to a deck will fail. 

*Not Yet Implemented: If this value is the guid of another card, that card will be added instead.

*Not Yet Implemented: If dependent is not a guid, and there exists a card that is not dependent, and whose Alternate points to this card, that card will be added instead. If more than one card satisfies these conditions, the user will be prompted to choose. If no such card exists, OCTGN will explode and we will blame the set creator. Do not make complex chains.

Special boolean: cardsRevertToOriginalOnGroupChange
This is a boolean value to be set in the Game Definition. If this is set to true... Do I really need to explain this? Just keep in mind, the table is considered a group.

As an example, 

<?xml version="1.0" encoding="utf-8"?>
<game name="Standard Playing Cards" id="8f437fff-fc8f-4abf-a2e6-f77ebff2ac6d" octgnVersion="3.0.0.8" version="1.1.3" markersize="25" cardsRevertToOriginalOnGroupChange="true" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <scripts>


Some possible uses of this function would be:
To maintain some type of evolution that occurs in a distinct state-based fashion, whether cyclically or not.
To handle visible cards with a non-generic backing, such as cards with rules or advertisements printed on the back (please remember isFaceUp will deal with visibility, not this usage)
To divide cards with multiple purposes into individual cards

For a working example of these features, please see https://github.com/MagnusVortex/Standard-Playing-Cards---OCTGN/tree/Alternate