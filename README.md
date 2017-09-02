CODE OR DIE
===========

Game engine for the "code or die" collaborative-coding domination game.

MOTIVATION
==========

Create a game...
- can be played in a 3-hour [NYC Python](http://meetup.com/nycpython) event night.
- can be played with multiple teams sitting in different conference rooms
- requires human interaction
- requires coding (from an automation perspective: scripts, reports, bots)
- is newbie-friendly

STORY
=====

In the year 10328, the Titanus people first ventured out from thir homeworld Titanus Earth. It was the year they discovered faster-than-light travel, through the warping of subspace. But barely had they sent their first expedition to what they would call New Earth, did Titanian scientists make two discoveries that would change the galaxy:

- the hyperrelay, a method of instanteous communication to any point in the galaxy
- the beam motivator, a city-sized distortion generator that would allow near-instanteous transport along a tightly calibrated corridor of warped subspace

It was a time of great hope for the Titanian people. Dreaming of a pan-Galactic Titanian empire, the people of Titanus Earth joined together to face their destiny as a unified peoples.

And little did they know... that the beam motivator, the tool that would enable them to grow and prosper, didn't merely warp subspace. With each warp, subspace would bend, it might tear, or it invert, but it would always eventually destory.

And little did they know... they weren't the only explorers.

GAME MECHANICS
==============
At the start of the game, teams control two systems:
- their home planet
- one remote output

Teams lose if:
- their home planet is sacked or destroyed

The game is over if:
- all but one team is defeated
- a predetermined time limit expires (representing catastrophic, galactic subspace inversion)
- all subspace returns to normal

The winning team is the team that controls the most systems at the end of the game.

The game is played in real-time via JSON API requests against this game engine.

Players control individual ship units and solar system outpots via API requests.

MAP
---

The map can be autogenerated as a `networkx` graph. The map should be large enough to force players to eventually build coding tools to automate control of their units. At first, these coding tools may be simple shell scripts to automate API calls. Teams may want to build reporting scripts or web frontends to better represent information. Advanced teams may want to create bots to automate unit movement.

The galaxy map is a collection of nodes (planets/solar systems) and edges, representing possible FTL travel between.

Nodes are weighted by their productive capacity. Edges are weighted by FTL travel time in seconds.

Teams have complete visibility of any systems they control. Teams can see all adjacent nodes to any systems they control.

Teams have complete visibility of any systems where they have units positioned. Teams have no adjacent-node visibility unless they control a system.

Visibilty allows teams to see
- total unit strength of all parties present
- current controlling party of a system (namely: the controller of its beam motivator)

SHIPS
-----

All ships are identical.

Ships can be instructed to travel to a destination (node-by-node fast-travel) via FTL or beam.

Beam travel is nearly instantaneous. FTL travel time is based on edge weights. All travel is node-by-node. Travel destination must be specified on an immediate-next-node basis.

Ship actions belong to a queue (to facilitate multi-hop travel and complex actions.) Queued actions can be canceled.

Ships can be instructed to participate in combat. Combat is locked to 30 second rounds. Combat occurs on a regiment-vs-regiment basis. Ship strength between combat parties is compared. Winner is party with greater strength of forces (in number of ships.) At end of combat round, loser loses 10% of total unit strength plus 10% for each double of attacker strength.

e.g., A and B engage in combat. A has 55 ships. B has 50 ships. A has more ships and is the winner. B has fewer ships and is the loser. B loses 10% of total unit strength (5 ships.) A has 55 ships remaining. B has 50-5=45 ships remaining.

e.g., A and B engage in combat. A has 100 ships. B has 50 ships. A has more ships and is the winner. B has fewer ships and is the loser. B loses 10% of total unit strength (5 ships) and another 10% of total unit strength, because A has double the number of ships. A has 100 ships remaining. B has 50-5-5=40 ships remaining.

**TODO:** can winner ever lose ships?
**TODO:** how do we do >2 party combat?

SOLAR SYSTEM OUTPOSTS
---------------------

Solar system outposts can be controlled by teams. Teams can take control of any uncontrolled outpost. Teams can release control of any outpost at any time. Teams can contest control of an outpost currently controlled by another party.

When contesting control of a system, the party with the most ships present gains control. Only the party controlling the system and the party contesting control are considered.

**IDEA:** upon gaining control of an solar system outpost, controlling party can see beam tuning parameters for every previous transit.Can they also see all past transit records? This means teams will have to "hide" their homeworlds.

e.g., Team A controls New Earth II and has 100 ships stationed there. Team B, an ally, moves 10 ships to New Earth II. Team A can relase control of system. Team B can immediately take control of system, as it is uncontrolled. Team C moves 50 ships to New Earth II. Team C can contest and win control of New Earth II, because Team C has 50 ships vs current controlling Team B's 10 ships. Team A can immediately contest control from Team C, with team A winning, because it has the most ships.

**TODO:** You must control a solar system or be granted transit authority to use its beam motivator?

BEAM MOTIVATORS
---------------

Ships can attempt near-instantaneously travel between any two beam motivators. Beam motivators create delicately warped corridors of subspace, and must be perfectly tuned to work correctly.

Ships travel on a unit-by-unit basis; they must be commanded individually.

All beams have unique tuning parameters that can be changed at any time.

Upon submitting an API request to travel from one beam motivator to another, the travelling ship must provide the tuning parameters for the destination beam.

If the tuning parameters for the destination beam are within <%5 tolerance, then transit occurs normally.

If the tuning parameters for the destination beam are between 5% and 50% tolerance, then the transiting ship is immediately destroyed, but both beam stations are left intact.

*TODO:* Remove the following rules about bad tuning parameters? Why did I add them in the first place?

If the tuning parameters for the destination beam are between 50% and 99% tolerance, then the transiting ship and all other untis at the source beam station are immediately destroyed. The source beam station does not become uncontrolled at this point. If the tuning parameters are 100% incorrect (totally inverted,) then the ship and the source beam station are immediately destroyed and a subspace inversion occurs at the source beam station, completely removing that system from the game map.

If 50% of all solar systems are destroyed, this triggers subspace inversion cascade. Cascade travels along the beam network at FTL-speeds until it spreads to the entire map. Catastrophic galactical subspace inversion. Game over.

Beam stations can spontaneously face subspace inversion on normal, successful beam transit. This occurs randomly. The probability of subspace inversion is not ever known to the user. Every successful beam transit increases the probability of random subspace inversion.

Upon subspace inversion, subspace tears travel along the beam etwork (at a high decay rate) affecting all other solar systems. Subspace tears also travel to every station which has travelled to or from the inverted station (at a lower but still high decay rate.)

Subspace damage can be repaired by setting beam motivator to REPAIR mode. This slowly rewinds previous subspace damage. In repair mode, ships cannot transit FROM the solar system outpost by beam. In repair mode, the outpost can be transited TO from any other outpost by beam without the need for tuning parameters. Transit TO a beam motivator in repair mode causes aggravated subspace damage.

**TODO:** what does the random inversion curve look like? It should start very slow but accelerate quickly to precipitate end-game.

**TODO:** what does the beam repair curve look like? It should start very slow, accelerating slowly. It should be reset in the event of beam transit.

**TODO:** can teams see inversion "waves" incident on their controlling station (thus knowing that someone lost an outpost, and, based on the strength, approximately where that outpost was)?

**TODO:** what does the decay rate look like? Penalize beam-connected stations more than FTL-connected stations. Decay rate should drop off very quickly (but still travel network-wide.) Decay rate via FTL and via beam-connectivity are cumulative. This should eventually penalise beam use, because everyone's home planet will be beam-connected to SOMEWHERE in the network.

**TODO:** what do tuning parameters look like? Should it be possible to (slowly) brute force them?

HYPERRELAY
----------

Teams can communicate via hyperrelay at any time. Hyperrelay is represented by individual team members ("diplomats") carrying physical materials (such as USB sticks) to other teams.

The USB sticks can contain any information.

Diplomats can also communicate verbally with other team members in order to strike deals, form alliances, trade beam tuning parameters. Diplomats cannot communicate with their home planet without physically returning to their team's area: they must work autonomously; they cannot merely relay messages between their leadership and other teams.

Players can contact referees at any time in the game, via communication channels established prior to game start. These communication channels should include low-visibility or covert options, such as SMS or e-mail.

Players are forbidden from forging messages to referees from other players.

At any time, a player may send a message to a referee asking to change teams. A player may rescind a team-change notification at any time.

Players who change teams too frequently or who change teams too close to game end may be penalized by referees.

SUMMARY of API ENDPOINTS
========================

Teams are given a unique API key. API keys may be changed at any time. Teams may decide how to distribute API keys.

`http POST token/`
PUT new team token.

`http GET systems/`
GET all systems currently controlled by team

`http GET systems/<id:int|name:str>/`
GET systems with given ID or name.
If team does not control solar system outpost or has no ships stationed at system, returns unavailable result.
Otherwise, if team has ships stationed at outpost, returns result including:
- convenience name of solar system
- current controller of solar system
- all teams and number of ships stationed at system (**NOTE**: can only see total troop strength, not individual unit information, because who cares? all units identical)
- the system's production capacity
Additionally, if team controls solar system outpost, return:
- all historical destination tuning parameters for this system's beam motivator
- all tuning parameters for beam transits originating from this outpost (**TODO:** include this or not? these are historical tuning parameters and may be not be up-to-date)
- IDs and names of all immediately adjacent systems, including FTL transit time
- IDs and names of all beam destinations (**TODO:** include this or not? include transit history, too? how many ships transitted and at what time?)

`http GET systems/names/`
GET all convenience names for all systems.

`http PUT system/<id:int|name:str>/name/`
PUT a convenience name on an systems. Team does not have to control an systems to give it a convenience name.

`http POST system/<id:int|name:str>/tuning`
PUT a new set of tuning paramters on an systems. Payload may indicate to use specific parameters or to generate random parameters.

`http GET system/<id:int|name:str>/orders`
GET a controlled system's order queue.

`http PUT system/<id:int|name:str>/orders`
PUT an order onto the end of a controlled systems's order queue. Returns failure if team does not control systems.

`http DELETE system/<id:int|name:str>/orders`
DELETE (clear) all orders from systems's order queue.

`http DELETE system/<id:int|name:str>/orders/<order:int>`
DELETE specified order from systems's order queue.

`http GET ships/`
GET all information for all units controlled by team.

`http GET ship/<id:int>`
GET all information for a given ship controlled by team. Information includes:
- ship convenience name
- ship location
- queue of ship orders
- ship status (live/destroyed)
- all messages sent to this ship

`http PUT ship/<id:int>/name`
PUT a convenience name on a ship. Ship does not have to be active to be given a convenience name.

`http GET ship/<id:int|name:str>/orders`
GET ship's order queue.

`http PUT ship/<id:int|name:str>/orders`
PUT a new order into at the end of a ship's order queue.

`http DELETE ship/<id:int|name:str>/orders`
DELETE (clear) all orders from ship's order queue.

`http DELETE ship/<id:int|name:str>/orders/<order:int>`
DELETE specified order from ship's order queue.

### solar system outpost order payloads:
`{ "order": "build", "count": COUNT, "team": [TEAM, ...] }`
produce count ship for specified teams (can produce ship for other teams)

`{ "order": "repair-mode" }`
set outpost beam motivator to repair mode

`{ "order": "transit-mode" }`
set outpost beam motivator to transit mode

`{ "order": "abandon" }`
abandon control of solar system outpost

`{ "order": "grant", "team": [TEAM, ...] }`
grant transit authority via beam motivator to teams

`{ "order": "revoke", "team": [TEAM, ...] }`
grant transit authority via beam motivator to teams

### unit order payloads:

`{ "order": "attack", "team": [TEAM, ...] }`
participate in combat against target team

`{ "order": "beam", "destination": ID or NAME, "tuning": TUNING }`
travel via beam to destination, using provided tuning parameters

`{ "order": "ftl", "destination": ID or NAME }`
travel via beam to destination, using provided tuning parameters

`{ "order": "sieze" }`
attempt to seize control of solar system outpost currently stationed at

`{ "order": "suicide" }`
self-destruct

**TODO**: send API key as header?
**TODO**: can you tell if a solar system is destroyed other than by attempting to travel to it via FTL? What happens to the unit if the solar system is destroyed?

TODO
====
- what's the optimal map size such that teams can play the first rounds manually but will be forced to play later rounds with some automation mechanisms?
- playtest
- better name?
- more sophisticated unit mechanics?
