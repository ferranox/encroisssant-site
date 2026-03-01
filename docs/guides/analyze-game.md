# Analyze Game

En Croissant gives you two complementary ways to analyze a chess game: **live engine analysis** for real-time evaluation as you step through moves, and **game reports** for a full annotated summary of an entire game.

## Before You Start

Make sure you have at least one engine installed and enabled. If you haven't done this yet, see the [Configure Engines](/docs/guides/configure-engines) guide. For game reports that detect novelties, you'll also want a [reference database](/docs/assets/databases) selected.

## Live Engine Analysis

Once a game is open, switch to the **Analysis** tab. Enable an engine from the panel on the right and you'll immediately start seeing evaluations and best-move suggestions for the current position as you navigate through the game. See the [Configure Engines](/docs/guides/configure-engines) guide for how to customize engine settings.

### Tablebase

For endgame positions with 7 or fewer pieces, En Croissant will automatically query the Lichess tablebase API and show you the theoretical result. This let's you check if an endgame is won, lost, or drawn with perfect play.

## Generating a Game Report

For a full annotated analysis of an entire game, click the `Generate Report` button in the Analysis panel. A dialog will appear where you can choose the engine and the following options:

### Reversed Analysis

Analyzes the game starting from the final position and working backwards. This is recommended because chess engines can reuse information from positions they've already evaluated, making the overall analysis faster and more consistent.

### Annotate Novelties

When enabled, the first position not found in your reference database will be marked with a novelty annotation. Requires a reference database to be selected.

### Show Variations

When enabled, the report will include the engine's best continuation line for any move annotated as a mistake.

## How Annotations Are Chosen

The report uses several metrics to determine the annotation for each move.

### Win% Loss

For each move, the engine's centipawn evaluation is converted into a win probability using the same formula as Lichess:

`Win% = 50 + 50 * (2 / (1 + exp(-0.00368208 * centipawns)) - 1)`

The drop in win% between the best move and the played move is the primary metric for mistake severity.

### Only Sound Move

The engine always runs with at least 2 lines (MultiPV 2) to check whether the best move is the *only* sound one, meaning all other moves are significantly worse. 

### Sacrifice

Detected using a lightweight Alpha-Beta search. If a move results in a materially worse position but the engine still considers it the best move, it's flagged as a sacrifice.

### Annotation Table

| Annotation | Requirements |
| --- | --- |
| <span style="color: #0d8599">**Brilliant (!!)**</span> | sacrifice that is the only sound move |
| <span style="color: #0d9268">**Good (!)**</span> | only sound move that punishes an opponent's mistake |
| <span style="color: #66a80d">**Interesting (!?)**</span> | sacrifice that isn't the only sound move |
| <span style="color: #f08c00">**Dubious (?!)**</span> | 5..10 win% loss |
| <span style="color: #e8580d">**Mistake (?)**</span> | 10..20 win% loss |
| <span style="color: #e03232">**Blunder (??)**</span> | 20..100 win% loss |
