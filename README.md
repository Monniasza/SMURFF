# SMURFF: Simple Module adjUstments for Real-ish Fuel-mass Fractions

*For people who want real-ish fuel mass fractions without too much hassle.  Formerly "Simple Mass adjUstments for Real-ish Fuel-mass Fractions."*

![SMURFF logo](https://github.com/Kerbas-ad-astra/SMURFF/raw/master/SMURFF%20logo.png)

## Features

SMURFF is a Module Manager patch that reduces the dry masses of fuel tanks and engines and improves engine performance so that KSP rockets can have fuel mass fractions closer to what we make in the real world, without going into per-part configurations like e.g. Realism Overhaul.

Most of a real rocket's structural strength comes from the fuel inside of it, rather than the tanks themselves -- the "tank" is just a metal shell to hold the fuel together.  KSP fuel tanks, on the other hand, seem to be made of lead plates and rebar.  This is all well and good for a planet where you only need 3.5 km/s to get to orbit, but it's not a lot of fun for the real solar system.  To help ease the transition to RSS (without requiring per-part configurations, thus allowing *any* mods to be supported), SMURFF makes several adjustments to engines, tanks, crew modules, and heat shields.  These adjustments are calibrated so that stock and stockalike-balanced parts will have their performance (TWR, fuel mass fraction, and ballistic coefficient) improved to be "real-ish" -- representative of typical aerospace technology of the last few decades, so that rockets built in RSS or 10X have mass fractions similar to real rockets.  You can reduce the buffs applied to parts (without tinkering with the mathematics) by reducing the values of "**tanklever**", "**enginelever**", and "**podlever**" at the top of SMURFF.cfg (or with a patch).  When they're set to 1 (as they are by default), the adjustments are made at their full values, and when they're set to 0, parts are left at stock performance values.  In-between is in-between, so those interested in intermediate-difficulty solar systems may want to experiment with setting the values to 0.5.

There are some built-in protections to prevent part masses from being sent into negative values, but otherwise, SMURFF will patch parts as if their mass and thrust values are balanced with stock parts.  If you want to exclude parts from being patched (e.g. because they already have real-ish TWR), include "**SMURFFExclude = true**" in the top-level config.  To avoid griping from KSP when SMURFF is not installed, you can use a patch like this one:

```
@PART[whatever_parts]:NEEDS[SMURFF]
{
	%SMURFFExclude = true
}
```

The specific adjustments are as follows:

- Stock and stockalike liquid fuel tanks (LFO, LF, Monoprop) have a fuel mass fraction of 87-89%; SMURFF increases this to 97% by reducing dry mass by 76%, putting them in line with real spacecraft fuel tanks.
	- Tanks that have fuel-switching patches from Stock Fuel Switch will get the appropriate mass buff for their equivalent Liquid Fuel and Oxidizer contents.
	- Other parts with switchable fuel tanks get their dry masses divided by 4.2, except for parts which are switchable in-flight, which only get a 50% dry mass reduction.
- Xenon gas tanks are adjusted from 75% to 90%.  I've seen some NASA sources saying xenon tanks are 95% fuel (or suggesting that they will soon be so), others suggesting 85%, so I went with 90%.
- Ore tanks are adjusted from 86% to 95% ore-by-mass (70% reduction in dry mass), to baseline against 55-gallon drums.
- Several mod resources are also handled (mostly from Nertea's various addons, since those have enough parts to produce a consistent balance).
	- LH2 tanks get their mass fractions adjusted from 83% to 78% (a slight *increase* in dry mass).  Because of the LFO buff, hydrolox "lifter" fuel tanks still go from 84% to 93% fuel by mass.
	- The zero-boil-off power requirement is increased by a factor of 24, so they now require 1.2 EC/sec per 1000 L of hydrogen stored.  (That's over 1000 EC/sec for the largest LH2 tank.  The best way I can think of to handle that is with a dedicated 2.5m nuclear reactor.)  Real zero-boil-off systems also require a heavy mass investment in passive thermal isolation and dedicated cryocooling systems, so it wouldn't be possible to use a rocket tank for zero-boil-off storage just by connecting it to a nuclear reactor, but this is one place where I bent for gameplay reasons.
	- Argon gas tanks from Near Future Propulsion are adjusted from 75% to 62% fuel (an *increase* in dry mass).  In stock, they match the efficiency of xenon tanks for gameplay balance reasons, but in reality, xenon tanks are much more efficient, and argon not nearly as much (xenon atoms are heavier, so a much greater mass of xenon can be packed into a given tank design).  Argon is more abundant (and thus cheaper) than xenon by orders of magnitude, so for large missions, the reduced fuel cost may be enough to make up for the mass penalty.
	- If anyone has a reference for the mass-efficiency of lithium storage and vaporization systems for spacecraft, I'd be happy to hear it.  :)  Until then, I'm buffing them to 87% fuel, which corresponds to a volume density between LFO/MP and LH2.
	- Karbonite is not handled at this time, as its tanks' mass efficiencies are already better than 95%.
- Bipropellant (LFO and LH2/Ox -- or anything with Oxidizer) rocket engines' thrusts are increased by 50%.  Any that are not crew containers or multi-mode engines also get a 62.5% reduction in mass.  Comparing the stock LFO engines to real RP-1/LOX rockets (as opposed to the LH2/LOX SSME, as I did previously), the "orbital" (i.e. stackable) stock engines have TWRs from 15-25, while real engines have TWRs from 60-100 (and getting up to 150 nowadays), so the TWRs need to be quadrupled.  The Skipper is actually a pretty good analog to the Merlin (several hundred kN of thrust, 2-3 meters tall), but rather than just cutting the weight to match TWRs, I increase the thrust so that stock rockets don't need ridiculous numbers of engines (recall that SpaceX uses nine Merlins on their first stages -- and incidentally, it turns out that SpaceY engines get their TWRs pushed into the 120-150 range, just like SpaceX's).
	- SRBs (baselining with the Kickback compared to the Space Shuttle SRBs) get their dry masses reduced by 40% and specific impulses get buffed by 40 seconds.  Their thrusts are left unchanged -- fuel density and TWR are already close to reality.
	- Nuclear thermal rockets, jet engines, monopropellant rockets, and ion thrusters are left alone.  (Oxidizer-burning modes of multi-mode engines still get the thrust buff.)
- Crew containers (pods, cockpits, cabins, etc.) and heat shields have their masses reduced by half (and their pyrolysisLossFactor doubled).  I justify this by looking at the mass-per-passenger of space capsules and jet liners, ballistic coefficents, and comparing crew cabins to fuel tanks of the same size.  For example, the Mk1-2 command pod weighs as much as the Apollo Command Module despite being nearly 40% smaller (by diameter -- 26% of the volume) and carrying half-sized astronauts.  The Mk1 and Mk2 crew cabins weigh as much as a full fuel tank of the same size, despite being mostly air-filled.  Same for heat shields -- the Apollo heat shield was 3.9 meters across and weighed 850 kg, vs. 2800 kg for the stock 3.75m heat shield.  (The reason that I didn't cut heat shield masses by 70% is so that shields can still be used for landing larger masses than just a capsule.)
- Most of the patches stack (e.g. a service module with both monoprop and liquid fuel will have its mass reduced appropriately), but not the ones that divide tank mass by a factor (crew capsule, bipropellant engine, and general-purpose switchable tank) -- only one of those may apply to any part, and they get applied in that order.

The result is that rockets have more Earth-like mass fractions and thus are able to achieve Earth-like payload masses without requiring the construction of horrible asparagus monsters.  To compare: with stock part masses, to get the Mk1-2 capsule (plus parachute and heat shield) up into space, I need to convert the Kerbal X into the "Kerbal X Triple-Heavy" (or "Kerbal 7"), with 6 asparagus-staged size 2 boosters identical to the core (actually, identical plus the round X200-6R tank from TurboNisuReloaded) and an extended upper-stage tank (an X200-32 instead of the stock -16).  It gets just shy of 7 tons into LEO (not counting the spent upper stage) with a 465-ton rocket (not counting the payload -- almost 70 times as much rocket as payload) which blows up the launchpad at takeoff.  With SMURFF, I just have to make the Kerbal X into the "Kerbal Xtended" by doubling the upper-stage fuel tank (the stock Kerbal X is entirely capable of getting into orbit on its own, but the extension is necessary to add the heat shield...), putting 3.52 tons into orbit (not counting the spent upper stage) with a 113.9-ton rocket (not counting the payload -- 32.36 times as much rocket as payload).   The Falcon 9 v1 got 10.5 tons into orbit and weighed 335 tons (31.9 times as much rocket as payload), using slightly less efficient engines than the Skipper and Poodle, so I figure I'm close enough for Kerbal work.

## Dependencies

SMURFF depends on [**Module Manager**](http://forum.kerbalspaceprogram.com/threads/55219) version **2.6.16 or later** to function.

## Recommendations

SMURFF is mainly intended for use with [**Real Solar System**](http://forum.kerbalspaceprogram.com/threads/55145).  It's why I made it, and that's where the default balance is set.  Try setting the levers to 0.5 for intermediate solar systems, like 64K or SKY.

Big rocket fractions (i.e. 1 kg into LEO = 25+ kg of rocket) call for big rockets, so [**SpaceY**](http://forum.kerbalspaceprogram.com/threads/100408) and [**SpaceY Expanded**](http://forum.kerbalspaceprogram.com/threads/133301) (alternatively, [**Behemoth Aerospace Engineering**](http://forum.kerbalspaceprogram.com/threads/124064) or [**Near Future Launch Vehicles**](https://kerbal.curseforge.com/projects/near-future-launch-vehicles)) are recommended to get big rockets without big part counts.  1.875m rocket parts from [**Home-Grown Rockets**](http://forum.kerbalspaceprogram.com/index.php?/topic/131556-104-5-hgr-community-fixes-home-grown-fixes-for-home-grown-rockets-v12-2016-mar-01/) (maintained by yours truly), [**MOLE**](http://forum.kerbalspaceprogram.com/index.php?/topic/94352-alpha-11-mark-one-laboratory-extensions-mole-v0503/), and the [**Making History**](https://kerbalspaceprogram.com/makinghistoryexpansion.php) expansion are also great for large upper stages and for payloads that are just too big for 1.25m rockets, but where 2.5m is overkill.  (Since thrust increases with the square of scale, and mass with the cube, all else being equal, the jump from 1.25 to 2.5 is proportionally much steeper than the jump from 2.5 to 3.75, so I've found 1.875m parts to be surprisingly handy.)

Of course, the other way around needing massive launch vehicles is to make smaller probes, so I've also gotten quite a bit of mileage out of [**RLA (Reborn)**](https://forum.kerbalspaceprogram.com/index.php?/topic/175512-14-rla-reborn/) and [**Modular Rocket Systems**](https://forum.kerbalspaceprogram.com/index.php?/topic/83959-1213-modular-rocket-systems-v1131-2016-10-21-stock-alike-parts-pack/).  (Most of that mileage is from the "Sparkler" engine in MRS.)

## Suggestions

Other addons that bring "real-ish" capabilities and challenges to Kerbal Space Program include:

- [**SCANSat**](http://forum.kerbalspaceprogram.com/threads/80369), to make biome and elevation maps globally available (vs. the limited-view and limited-access KerbNet system) and optionally require just a bit more effort when scanning for resources.
	- [**DMagic Orbital Science**](https://forum.kerbalspaceprogram.com/index.php?/topic/59009-13x-dmagic-orbital-science-new-science-parts-v1310-6132017/), [**Impact!**](https://forum.kerbalspaceprogram.com/index.php?/topic/102771-13x-impact-impact-science-and-contracts-v160/), MOLE, [**Nehemiah Engineering Orbital Science**](https://forum.kerbalspaceprogram.com/index.php?/topic/106924-13-nehemia-engineering-orbital-science-mods-neos-michas-build-07b2/), and the [**Breaking Ground**](https://www.kerbalspaceprogram.com/game/breakingground/) expansion add science experiments that have actually been run in space.  [**Station Science**](https://forum.kerbalspaceprogram.com/index.php?/topic/154629-130-station-science-continued-v230/) is in the same vein, although it's a little more on the 'fantastic' side.  (If you're going to run most or all of these, you should probably use an expanded tech tree or dial back science returns in the difficulty options, to preserve game balance.)
- [**USI Life Support**](http://forum.kerbalspaceprogram.com/threads/116790), for a life support system which is simple and forgiving (unless you configure it to kill Kerbals).
	- [**USI Kolonization Systems**](http://forum.kerbalspaceprogram.com/threads/79588) and/or [**Pathfinder**](http://forum.kerbalspaceprogram.com/index.php?/topic/121397-113-pathfinder-100-space-camping-geoscience/), for ISRU that allows self-sustaining colonies (with some effort).
	- You should also consider picking up [**Global Construction**](https://forum.kerbalspaceprogram.com/index.php?/topic/154167-17-global-construction/) and/or [**Extraplanetary Launchpads**](http://forum.kerbalspaceprogram.com/threads/59545) and [**OSE Workshop**](http://forum.kerbalspaceprogram.com/threads/108234) (and [**Kerbal Inventory System**](http://forum.kerbalspaceprogram.com/threads/113111), OSE's dependency), as an extension of NASA's real-world interest in in-situ manufacturing and repair.  (And to give your bases something new to do.)
	- To (loosely) simulate long-term physical impacts of microgravity, there's [**KeepFit**](http://forum.kerbalspaceprogram.com/index.php?/topic/61510-113wip-plugin0108627072016-keepfit-kerbal-fitness-degradation/).  (Be advised that it does require specific mods for "exercising" spaces.  Currently [Civilian Population](https://forum.kerbalspaceprogram.com/index.php?/topic/162204-13-civilian-population-209-university/), [CxAerospace](https://forum.kerbalspaceprogram.com/index.php?/topic/138910-13-cxaerospace-stations-parts-pack-v162-2017-5-24/), [DSEV](https://forum.kerbalspaceprogram.com/index.php?/topic/122162-13-deep-space-exploration-vessels-230-build-nasa-inspired-ships-in-ksp/), [Stockalike Station Parts Redux](https://forum.kerbalspaceprogram.com/index.php?/topic/170211-131-stockalike-station-parts-redux-january-28th/), [SXT](https://forum.kerbalspaceprogram.com/index.php?/topic/151129-131-sxt-continued/), [Tokamak Industries](https://forum.kerbalspaceprogram.com/index.php?/topic/163166-13-tokamak-industries-refurbished-parts-featuring-porkjets-hab-pack/), and UKS are the only mods with "recognized" exercise rooms.  It is much more generous when it comes to mods with "neutral" habitation space, and any spacecraft which is landed on a planet with more than 0.05 G's counts as exercising as well.)
	- As an alternative to KeepFit and USI-LS's habitation effects, [KerbalHealth](https://forum.kerbalspaceprogram.com/index.php?/topic/155313-131-kerbal-health-11-2018-01-20-new-mechanics-rebalance-optimization-more-supported-mods/) looks promising.
- Nertea's [**Cryogenic Engines**](http://forum.kerbalspaceprogram.com/threads/117766), [**Kerbal Atomics**](http://forum.kerbalspaceprogram.com/index.php?/topic/130503-105-kerbal-atomics-fancy-nuclear-engines-20022016-fixes-nfe-support/), and [**Near Future Technologies**](http://forum.kerbalspaceprogram.com/index.php?/topic/47786-105-near-future-technologies-merry-christmas-updated-all-to-105/) provide cryogenic, nuclear thermal, and ion and plasma propulsion systems, as well as a simple boiloff and boiloff mitigation mechanism for liquid hydrogen.  SMURFF will adjust their respective fuel tanks to match their actual or predicted performance.  (Except for lithium, which I haven't found any references for -- it gets adjusted, but it's largely a guess on my part.)
- For more realistic handling of engine throttles and attitude control (because real rocket engines can't be throttled down to 1% of their maximum thrust), check out [**Throttle Limit Extended**](https://forum.kerbalspaceprogram.com/index.php?/topic/169352-131-throttle-limit-extended/) and [**MandatoryRCS**](https://forum.kerbalspaceprogram.com/index.php?/topic/154658-131-mandatoryrcs-14-part-pack-10-reaction-wheels-nerf-sas-rotation-persistence/) (I recommend turning on the option that permits reaction wheels in crewed pods to operate, since otherwise that's just *yet another* part you'll have to add to a vessel).

Feel free to suggest other "real-ish" addons!  To give you some idea of what I'm looking for, addons suggested with SMURFF shall adhere to the following criteria:

1. They shall not require individual parts to be configured in order to function (as e.g. RealFuels does) -- if an addon can't work out-of-the-box with any other addons that people use, it doesn't belong on the list.
2. They shall improve the realism of some aspect of KSP that is not very or not at all realistic.  (KSP has no life support whatsoever, so USI-LS is included, but it has mildly realistic aerodynamic and heating systems, so FAR and Deadly Reentry are not.)

Of course, criterion zero is that I won't suggest an addon that I don't like and use (or have used or considered using) myself.  :)

## Download and install

- [**GitHub**](https://github.com/Kerbas-ad-astra/SMURFF/releases)
- CurseForge

From there, just unzip the "SMURFF" folder into your GameData directory.

Please let me know in [**the forum thread**](http://forum.kerbalspaceprogram.com/threads/131023) or on [**the GitHub issue tracker**](https://github.com/Kerbas-ad-astra/SMURFF/issues) if you find any issues!

## Version history and changelog

- 2015 08 08: Initial release.
- 2015 11 19 (1.1): Renamed to Simple **Module** adjUstments for Real-ish Fuel-mass Fractions (since we're not just touching mass anymore).
	- +50% to non-SRB engine thrusts and -62.5% to mass, to bring TWRs in line with RP-1/LOX engines and reduce need for ridiculous engine clusters.
	- +40 seconds to SRB Isp, to bring Kickbacks in line with Space Shuttle SRBs.
	- Mass reduction of resource containers is now proportional to their contents, so parts which have resources and do other things (e.g. command pods, wings with fuel) only get mass reduction corresponding to the part of them which holds resources (e.g. command pods are now slightly lighter because of their monopropellant storage, but not by a factor of 4).
	- New patch to act on Procedural Parts.
	- Based on RealFuels data and further research, LH2 and argon mass fractions no longer improved.
	- Adjusted patch to properly grab all engine modules.
	- Compatible with Stock Fuel Switch and Cryogenic Engines.
		- **If you replaced or modified Cryogenic Engines's fuel switcher patch, make sure you restore it to the original -- otherwise, tanks might get double-buffed.**
- 2015 11 20 (1.1.1): Minor bug fixes.
	- Fixed Procedural Parts patch (thanks to speedwaystar).
	- Excluded air-breathing jets, nuclear thermal rockets, monopropellant engines, and ion engines from TWR buffs.  (They're close enough to reality already.)
- 2015 11 22 (1.1.2): More compatibility (inspired by Atomic Age).
	- Improved selectivity of engine mass buff by excluding multi-mode engines and IntakeAtm-breathing engines.  (All Oxidizer-burning engine modes will still get a thrust increase.)
- 2015 12 07 (1.1.3): More selection adjustments.
	- Changed engine mass buffs to apply to engines which only have oxidizer-burning modes (so that SpaceY's engines get buffed -- they use a MultiModeEngine module to simulate the all-nozzle vs. center-out/center-only modes of operation, so excluding all multi-mode engines wasn't fair.)
- 2016 01 08 (1.1.4): Argon adaptation
	- New ArgonGas patch for the tank efficiency changes made in Near Future Propulsion 0.6.0.
	- **I've made some slight changes in the backend for handling Cryogenic Engines.** In my testing, I haven't noticed any changes in spacecraft mass (either in the VAB or in flight), but please be careful!
- 2016 01 27 (1.2): Entry, Descent, and Landing
	- Masses of crew containers and heat shields are reduced by half, to permit reduced (and reasonable) capsule ballistic coefficients.  Now pods are more likely slow down in time to get their chutes open.
	- Added a "reserved mass" system so that engines and pods only modify mass which is not accounted for by fuel tanks and batteries.
	- Added a "SMURFFCONFIG" section -- adjust the "tanklever", "enginelever", and "podlever" variables to control how much of a buff is applied.  1 for real-ish performance (default), 0 to leave stock values alone, and everything in between is in between.  Try 0.5 for the likes of 64k or SKY.
	- Changed patches to run FOR[zSMURFF], so that the patches run later.  SpaceY's engines now get properly buffed when Cryogenic Engines is installed.  (SpaceY replaces the ModuleEnginesFX modules of some engines in that circumstance, and since SpaceY comes after SMURFF, the thrust buff was being overwritten.)
	- Added a "fix" for parts whose mass gets sent into the negatives (i.e. any part which weighs less than it "should" by stockalike standards): their masses get restored to their initial values.
	- Added support for "SMURFFExclude" -- add "SMURFFExclude = true" to any part you don't want to get buffed.
- 2016 02 02 (1.2.1): De-icing
	- Adapted to new Cryogenic Tanks LH2 mass fractions and tank setups.  (LH2 tanks actually get hit with the nerf-bat.)
- 2016 02 20 (1.2.2): Sore throat
	- Added some new patch logic to catch ablative engines.
- 2016 03 17 (1.3): Wild Blue Yonder
	- Parts with WBIResourceSwitcher (e.g. the Titan fuel tanks from MOLE) are now included in the switchable-tanks patches.
	- Switchable (FS and Interstellar Fuel Switch) tanks with different tankMasses now get proper mass buffs, thanks to the new array-editing features in Module Manager.
		- This feature is only present in MM version 2.6.16 or later, so **this version and later versions are not compatible with KSP 1.0.4.**
- 2016 05 17 (1.4): Inb4B9
	- Handles B9 Part Switcher, and the corresponding updates to Cryogenic Engines.
		- Also changed patches to run FOR[zzz_SMURFF], so that it comes after the cryotanks patch.
		- Because the corresponding version of Cryogenic Engines was released for KSP 1.1.2, **this version and later versions are not compatible with KSP 1.0.5, 1.1.0, or 1.1.1.**
	- Handles the tankResourceMassDivider variable from the generic IFS patch.
	- Lithium tanks now get buffed.
- 2016 05 26 (1.5): Ore Rly?
	- Ore tanks are now adjusted, baselined against 55-gallon drums.
	- Compatible with tank types included in Near Future Construction 0.6.2.
	- Added new "Extras" folder for patches that are too part-specific to be included in the main distribution.  The first Extra patch is for the CryoX and soft tanks from Ven's Stock Revamp.  I make no promises about their balance when used with CryoTanks or other fuel-switching plugins...
- 2016 05 28 (1.5.1): Mass Deregulation
	- Relaxed one of the "overdraft protection" checks.
- 2016 05 28 (1.5.2): Ore you sure?
	- Added procedural ore tank type.
- 2016 05 28 (1.5.3): B9-B
	- Made the CryoTank patches less brittle, to handle parts with multiple B9PartSwitch modules (e.g. Fuel Tanks Plus).
- 2016 08 15 (1.5.4): Tanks F-Ore Nothing
	- Added an "Extra" patch to fix the procedural ore tank.  If you're using Procedural Parts, drop the "ProcOreFixer.cfg" patch in your GameData folder (anywhere will do).	
	- The license is changed to the GPL v3 (or later).
- 02016 10 29 (1.5.5): Snow Miser
	- Updated CryoTanks patch to adapt to rebalance.
	- Because the corresponding version of CryoTanks was released for KSP 1.2, **this version and later versions are not compatible with KSP 1.1.2 or 1.1.3**.
- 02017 01 28 (1.6): Happy New Year
	- Removed "Extra" patch for the procedural ore tank, as they've accepted my PR to add it directly to the part config.
	- Engine patches now operate on minThrust as well as maxThrust (if someone should ever release an engine with nonzero minThrust).
	- Reduced reaction-wheel authority on command pods in proportion to their mass reduction.
	- Adjusted IFS patch. It works again, but note that IFS already has its own RSS patch, so SMURFF defers to that.
	- Added support for Mark IV tank types.  Because the corresponding version of Mark IV was released for KSP 1.2.2, **this version and later versions are not compatible with KSP 1.2.0 or 1.2.1**.
- 02017 02 24 (1.6.1): B9-C
	- Another tweak to the CryoTank patch, to avoid stepping into B9's CryoTank patches.
- 02017 04 10 (1.6.2): Kerbals Not Smurffs
	- Exclude Kerbals from SMURFF patching.
	- Add pure-LF IFS patch.
- 02018 02 09 (1.7.0): Zero Level
	- Adjusted tank math to remove divide-by-zero error.  Feel free to set tanklever = 0 now!
	- Rebalance of tank adjustment factors to adapt to changes in Near Future Propulsion and CryoTanks (and some in response to user feedback).  LFO and hydrolox tanks got a bit of a buff, most everything else stays put.  Because the corresponding versions of NFP and CryoTanks were released for KSP 1.3.1, **this version and later versions are not compatible with KSP 1.2.2 or 1.3.0**.
	- The "Extra" patch for Ven's Stock Revamp is more detailed, and now has different behavior for when CryoTanks is or is not installed.  Their performance is baselined to the Super Lightweight (Space Shuttle External) Tank.
- 02018 03 21 (1.8.0): Kerbal Equinox
	- Updated xenon patches to new balance of stock tanks.  Because those changes were made in KSP 1.4.0, **this version and later versions are not compatible with KSP 1.3.1**.
- 02019 05 19 (1.9.0): The Power of the Special
	- Vintage Kerbal suit variants (Making History) now excluded from patching.
	- Moved Ven's "Extra" patch into the main SMURFF folder, now called "Special".
	- Squad's new capsules (Mk1-3, Mk2, and Mk2 Lander Can) now have a "Special" patch, since they're much lighter than their predecessors.
	- Update ZBO power requirement patch and Procedural Parts patch to adapt to CryoTanks update.
	- New "Special" patch for Universal Storage II tanks.
	- New "Special" patch to handle Tundra Exploration fuel tanks.
- 02019 11 12 (1.9.1): Green Origin
	- Adding support for CryoTanks' liquid methane tank variants.  Because these were added for a version of CryoTanks made for KSP 1.7, **this version and later versions are not compatible with KSP 1.4 to 1.6.**
	- Futuristic Kerbal suit variants (Breaking Ground) now excluded from patching.

## Roadmap

As I get the time, I'll publish to CurseForge.

If you find any classes of parts (i.e. anything that can be described by using Module Manager to filter by module, resource, mass, etc.) that get horribly mistreated, I'll make an adjustment.  I might even make a special patch for a particular mod's parts, if it's not too much trouble on my part.

## Credits

Thanks are owed to NathanKell and the entire Realism Overhaul team for making an incredibly detailed, thoroughly researched and calculated system...and convincing me to make something simpler.  :wink:  The tank-mass spreadsheet was a handy resource!

Even more thanks to ialdeboath and sarbian for the power of Module Manager.  Seriously, half of my addons wouldn't exist if not for them.

## License

Simple Module adjUstments for Real-ish Fuel-mass Fractions ("SMURFF") is copyright 2015-2019 Kerbas_ad_astra.  Configuration files are released under the [**GPL v3 license**](https://www.gnu.org/licenses/gpl-3.0) (or any later version).  Any redistributions must use a different name and folder (per section 7c).  All other rights (e.g. the SMURFF logo) reserved.