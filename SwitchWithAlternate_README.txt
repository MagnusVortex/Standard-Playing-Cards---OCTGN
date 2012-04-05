SwitchWithAlternate() API
For a working example, please see https://github.com/MagnusVortex/Standard-Playing-Cards---OCTGN/tree/Alternate
The Six Degrees of Kevin Bacon cardset installs into the Standard Playing Cards gamedef. The BettyWhite+MerylStreep deck shows off the examples I will be using in this API.

Special optional card attribute: "alternate" 
This property is designed to accept the guid of another card. 

Special optional card attribute: "dependent"
If this parameter is defined to be a non-false value, the deck editor will not allow it to be placed into a deck. 


Python Changes:

2 new functions

isAlternate(int CardID)
SwitchWithAlternate(int CardID)

The default behavior of isAlternate(int id) is to return false.
The default behavior of SwitchWithAlternate(int id) is to do nothing.

In order to take advantage of this new functionality, a set definer must add a new attribute to each card for which he/she would like to enable these functions. The attribute must be named "Alternate", and its value must be the GUID of another card in the set. If these conditions are met, calling SwitchWithAlternate will effectively switch the current card with the predefined alternate version. The card's picture will change, and any custom card properties will be replaced with the values for the corresponding alternate version. After calling this function, isAlternate will return true.

As mentioned previously, calling SwitchWithAlternate() on a card without the "Alternate" property defined will do nothing, with the exception of a card that is the end of an alternate chain. If card A has card B as its alternate, and card B has no defined alternate, then we have a simple alternate chain: A->B. Calling switchWithAlternate() on A will turn it into B, and calling it again will turn it back into A. In this case, isAlternate will be true when B is the active version.

This is a trivial case, though. Alternate chains may be more complex if necessary.

Let me illustrate how this works. Consider the following Alternate Chains:

Betty White -> Sandra Bullock -> Kevin Bacon
                 Meryl Streep -> Kevin Bacon

isAlternate() will return true unless the original card is the active version. In the above example, Betty White and Meryl Streep would be the originals, and Sandra Bullock and Kevin Bacon are both alternates. Therefore, isAlternate() will always return true for Sandra Bullock and Kevin Bacon, but false for Betty White and Meryl Streep.

As illustrated above, multiple cards may have the same card identified as their alternate versions. OCTGN will return a card to its original state upon switching from a card whose alternate is undefined. In the above example, Kevin Bacon's alternate is undefined. This means he is the end of both chains.

This affect persists across all card groups, including the table. It is up to the game definer to decide the how best to use this functionality in the game. To ease the logic required by the scope of possibilities, there are some quality of life improvements:

Special Card Attribute: dependent (Subject to change: Not yet fully implemented.)

If this property is undefined, set to a zero length (empty) string, or set to some case-insensitive variant of "false", adding the card to a decklist from within the deck editor will behave as normal.
If this property is set to any other value, the deck editor will ensure that all attempts to add this card to a deck will fail. 

Special boolean: cardsRevertToOriginalOnGroupChange
This is a boolean value to be set in the Game Definition. If this is set to true... Do I really need to explain this? Just keep in mind, the table is considered a group.

As an example of how to use this, 

<?xml version="1.0" encoding="utf-8"?>
<game name="Standard Playing Cards" id="8f437fff-fc8f-4abf-a2e6-f77ebff2ac6d" octgnVersion="3.0.0.8" version="1.1.3" markersize="25" cardsRevertToOriginalOnGroupChange="true" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <scripts>


Some possible uses of this function would be:
To maintain some type of evolution that occurs in a distinct state-based fashion, whether cyclically or not.
To handle visible cards with a non-generic backing, such as cards with rules or advertisements printed on the back (please remember isFaceUp will deal with visibility, not this usage)
To divide a card with multiple purposes into individual cards

With a bit of cleverness, a crafty game developer could potentially hijack this functionality and use it for alternate art. I would recommend against doing this, however, as it would likely mean the inability to utilize the full functionality I've provided without drastic complications. Instead, I'd encourage a feature request for any alternate artwork support game devs find lacking.

For a working example of these features, please see https://github.com/MagnusVortex/Standard-Playing-Cards---OCTGN/tree/Alternate
The Six Degrees of Kevin Bacon cardset installs into the Standard Playing Cards gamedef. The BettyWhite+MerylStreep deck shows off the example chains from earlier.