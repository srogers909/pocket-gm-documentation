# Attribute Effects in Enhanced Play Simulation

This document explains how each player, staff, and referee attribute directly impacts play outcomes in the enhanced play simulation engine.

## Overview

The Enhanced Play Simulator (`play_simulator_enhanced.dart`) uses granular, attribute-based calculations instead of generic `overallRating` values. Every play outcome is determined by the specific attributes of the players, coaches, and referees involved in the situation.

## Player Attributes by Position

### Quarterback (QB)
Position attributes: `Pass Strength`, `Accuracy`, `Evasion`

#### Pass Strength
- **Effect**: Determines QB's ability to throw deep passes and overcome distance penalties
- **Usage**: 
  - Deep passes (>15 yards): Adds bonus to completion chance: `(armStrength - 50) / 200.0`
  - Field goal holder accuracy (indirectly affects special teams)
- **Range**: 0-100, where 50 is average
- **Impact**: Higher values enable more accurate long throws and better performance under pressure

#### Accuracy  
- **Effect**: Primary factor in pass completion probability
- **Usage**: 
  - Major completion modifier: `(qbAccuracy - 50) / 100.0`
  - Base completion rate starts at 40%, accuracy can swing this ±50%
- **Range**: 0-100, where 50 is average
- **Impact**: Most critical QB attribute - directly determines pass success rate

#### Evasion
- **Effect**: QB's ability to avoid sacks and extend plays under pressure
- **Usage**:
  - Sack avoidance: Modifies base 30% sack chance by `(qbEvasion - 50) / 200.0`
  - Sack probability range: 5-60% (clamped for game balance)
  - Higher evasion reduces sack likelihood when pressured
- **Range**: 0-100, where 50 is average
- **Impact**: Directly reduces sack probability - elite evasion (90+) can cut sack rate nearly in half

### Running Back (RB)
Position attributes: `Rush Power`, `Rush Speed`, `Evasion`

#### Rush Power
- **Effect**: Effectiveness in power running situations and short-yardage scenarios
- **Usage**:
  - Power runs: `(rbPower - 50) / 25.0` yards bonus
  - Inside runs: `(rbPower + rbEvasion - 100) / 50.0` yards bonus
- **Range**: 0-100, where 50 is average
- **Impact**: Critical for goal line and short-yardage situations

#### Rush Speed
- **Effect**: Effectiveness in outside runs and breakaway potential
- **Usage**:
  - Outside runs: `(rbSpeed - 50) / 25.0` yards bonus
  - Breakaway calculation: `(rbSpeed + rbEvasion - 100) / 200.0` chance
  - Big play potential when yards gained > 5
- **Range**: 0-100, where 50 is average
- **Impact**: Enables explosive plays and outside running success

#### Evasion
- **Effect**: Ability to break tackles, avoid defenders, and create additional yardage after contact
- **Usage**:
  - Inside runs: Combined with power `(rbPower + rbEvasion - 100) / 50.0` for balanced approach
  - Breakaway potential: Combined with speed `(rbSpeed + rbEvasion - 100) / 200.0` for explosive plays
  - Position-specific assignment: Only QB and RB positions receive evasion values equal to their positionRating3, all other positions get 0
- **Range**: 0-100, where 50 is average
- **Impact**: Increases big play probability, yards after contact, and enables RBs to create yardage even when initial blocking fails

### Wide Receiver (WR)
Position attributes: `Speed`, `Catching`, `Route Running`

#### Speed
- **Effect**: Ability to get open deep and create separation
- **Usage**:
  - Deep pass prioritization: Combined with route running for receiver selection
  - Coverage battle: `(receiverSpeed - defenderSpeed) / 300.0` completion bonus
  - Yards After Catch (YAC): `max(0, (receiverSpeed - defenderTackling) / 10)` bonus yards
- **Range**: 0-100, where 50 is average
- **Impact**: Critical for deep threats and YAC production

#### Catching
- **Effect**: Reliability in securing passes and avoiding drops
- **Usage**:
  - Completion probability: `(receiverCatching - 50) / 150.0` bonus
  - General pass prioritization when not deep routes
- **Range**: 0-100, where 50 is average
- **Impact**: Primary factor in pass completion reliability

#### Route Running
- **Effect**: Precision in running routes and getting open
- **Usage**:
  - Completion probability: `(receiverRouteRunning - 50) / 200.0` bonus
  - Deep pass receiver selection: Combined with speed
- **Range**: 0-100, where 50 is average
- **Impact**: Helps QB accuracy and creates separation

### Tight End (TE)
Position attributes: `Catching`, `Blocking`, `Speed`

#### Catching
- **Effect**: Same as WR catching ability
- **Usage**: Identical to WR catching mechanics
- **Impact**: Primary receiving attribute for tight ends

#### Blocking
- **Effect**: Contribution to offensive line protection
- **Usage**: Included in offensive line calculations when TE is blocking
- **Impact**: Improves pass protection and run blocking

#### Speed
- **Effect**: Same as WR speed mechanics
- **Usage**: Route running and YAC calculations
- **Impact**: Enables TE to be used as receiving threat

### Offensive Line (C, OG, G, OT, T, OL)
Position attributes: `Pass Blocking`, `Run Blocking`, `Strength`

#### Pass Blocking
- **Effect**: Protection against pass rush and sack prevention  
- **Usage**:
  - Team pass protection: Average of all OL `Pass Blocking` ratings
  - Pressure calculation: `max(0.0, (passRush - passProtection) / 100.0)`
  - Sack probability: 30% chance if pressured
- **Range**: 0-100, where 50 is average
- **Impact**: Directly prevents sacks and QB pressure

#### Run Blocking
- **Effect**: Creating running lanes and moving defensive players
- **Usage**:
  - Team run blocking: Average of all OL `Run Blocking` ratings
  - Yards calculation: `(runBlocking - runDefense) / 20.0` base yards
- **Range**: 0-100, where 50 is average
- **Impact**: Foundation of all running play success

#### Strength
- **Effect**: Physical power in blocking assignments and line battles
- **Usage**: 
  - Pass blocking: `(avgOLStrength - 50) / 4.0` modifier to pass protection (±12.5 point range)
  - Run blocking: `(avgOLStrength - 50) / 3.0` modifier to run blocking (±16.7 point range)
  - Combined with Pass/Run Blocking ratings to determine final offensive line effectiveness
- **Range**: 0-100, where 50 is average
- **Impact**: Provides significant bonus/penalty to blocking effectiveness, with greater impact on run blocking than pass blocking

### Defensive Line (DE, DT, NT, DL)
Position attributes: `Pass Rush`, `Run Defense`, `Strength`

#### Pass Rush
- **Effect**: Ability to pressure quarterback and generate sacks
- **Usage**:
  - Team pass rush: Average of all DL `Pass Rush` ratings
  - Pressure generation: Compared against offensive pass protection
- **Range**: 0-100, where 50 is average
- **Impact**: Creates sacks, hurries, and affects pass completion

#### Run Defense
- **Effect**: Stopping running plays and controlling gaps
- **Usage**:
  - Team run defense: Average of all DL `Run Defense` ratings
  - Run stopping: Reduces expected rushing yards
- **Range**: 0-100, where 50 is average
- **Impact**: Primary factor in limiting rushing success

#### Strength
- **Effect**: Physical power in line battles against offensive linemen
- **Usage**: 
  - Pass rush: `(avgDLStrength - 50) / 4.0` modifier to pass rush rating (±12.5 point range)
  - Run defense: `(avgDLStrength - 50) / 3.0` modifier to run defense rating (±16.7 point range)
  - Combined with Pass Rush/Run Defense ratings to determine final defensive line effectiveness
  - Only defensive linemen (DE, DT, NT, DL) contribute to strength calculations, not linebackers
- **Range**: 0-100, where 50 is average
- **Impact**: Provides significant bonus/penalty to defensive effectiveness, with greater impact on run defense than pass rush, directly opposing offensive line strength bonuses

### Linebacker (LB, ILB, MLB, OLB)
Position attributes: `Tackling`, `Coverage`, `Speed`

#### Tackling
- **Effect**: Ability to bring down ball carriers effectively
- **Usage**:
  - Run defense: LB tackling contributes to team run stopping
  - YAC prevention: Defender tackling reduces receiver YAC bonus
- **Range**: 0-100, where 50 is average
- **Impact**: Limits big plays and reduces yards after contact

#### Coverage
- **Effect**: Pass coverage ability against receivers
- **Usage**:
  - Defender selection: Best coverage defender matched against receivers
  - Completion reduction: `(defenderCoverage - 50) / 150.0` penalty to completion
- **Range**: 0-100, where 50 is average
- **Impact**: Critical in pass defense and completion prevention

#### Speed
- **Effect**: Pursuit and coverage range
- **Usage**:
  - Coverage battles: Speed differential affects completion probability
  - Currently factored into receiver vs defender matchups
- **Range**: 0-100, where 50 is average
- **Impact**: Enables better coverage and run pursuit

### Cornerback (CB)
Position attributes: `Coverage`, `Speed`, `Man Defense`

#### Coverage
- **Effect**: Primary pass coverage ability
- **Usage**: Same as linebacker coverage mechanics
- **Range**: 0-100, where 50 is average
- **Impact**: Most important CB attribute for pass defense

#### Speed
- **Effect**: Ability to stay with receivers
- **Usage**: Direct matchup against receiver speed in completion calculations
- **Range**: 0-100, where 50 is average
- **Impact**: Critical for covering fast receivers

#### Man Defense
- **Effect**: Specific man-to-man coverage skills
- **Usage**: Not yet implemented in current system
- **Range**: 0-100, where 50 is average
- **Impact**: Reserved for future coverage scheme implementations

### Safety (S, FS, SS)
Position attributes: `Coverage`, `Tackling`, `Speed`

#### Coverage
- **Effect**: Deep pass coverage and zone responsibility
- **Usage**: Same as other defensive backs
- **Range**: 0-100, where 50 is average
- **Impact**: Critical for preventing deep passes

#### Tackling
- **Effect**: Run support and tackle reliability
- **Usage**: Same as linebacker tackling mechanics
- **Range**: 0-100, where 50 is average
- **Impact**: Last line of defense against big plays

#### Speed
- **Effect**: Range and pursuit ability
- **Usage**: Coverage battles and run support
- **Range**: 0-100, where 50 is average
- **Impact**: Enables safety to cover large areas

### Kicker (K)
Position attributes: `Leg Strength`, `Accuracy`, `Consistency`

#### Leg Strength
- **Effect**: Maximum field goal range and distance capability
- **Usage**:
  - Range determination: If distance > leg strength, 30% penalty to success rate
  - Success rate bonus: `(legStrength - 50) / 200.0`
- **Range**: 0-100, where 50 is average
- **Impact**: Enables longer field goal attempts

#### Accuracy
- **Effect**: Precision in kick placement
- **Usage**:
  - Success rate modifier: `(accuracy - 50) / 150.0` bonus
- **Range**: 0-100, where 50 is average
- **Impact**: Primary factor in field goal success

#### Consistency
- **Effect**: Reliability under pressure
- **Usage**:
  - Success rate modifier: `(consistency - 50) / 200.0` bonus
- **Range**: 0-100, where 50 is average
- **Impact**: Reduces variance in performance

### Punter (P)
Position attributes: `Punt Power`, `Punt Accuracy`, `Pressure Resistance`

#### Punt Power
- **Effect**: Distance capability on punts
- **Usage**:
  - Distance calculation: Base 35 yards + `(puntPower - 50) / 2`
  - Final distance clamped between 20-65 yards
- **Range**: 0-100, where 50 is average
- **Impact**: Directly determines punt distance

#### Punt Accuracy
- **Effect**: Precision in punt placement and hang time
- **Usage**:
  - Return yardage: Poor accuracy `(50 - puntAccuracy) / 10` increases return yards
  - Affects field position battle
- **Range**: 0-100, where 50 is average
- **Impact**: Reduces return opportunities

#### Pressure Resistance
- **Effect**: Performance under defensive pressure during punt situations
- **Usage**: 
  - Pressure situation detection: Based on quarter ≥ 4, field position < 25 yards, or 4th down
  - Defensive pressure calculation: Uses defensive line rating + situational modifiers
  - Blocked punt probability: `max(0.05, min(0.30, (defensivePressure - 50) / 300.0))` modified by pressure resistance
  - Distance consistency: Higher pressure resistance maintains more consistent punt distances under pressure
  - Applied in `_simulatePunt` method when pressure situations are detected
- **Range**: 0-100, where 50 is average
- **Impact**: Reduces blocked punt chance and maintains performance consistency in high-pressure situations. Elite punters (87+ rating) show measurably better distance consistency and lower block rates compared to average punters (62 rating) especially in extreme pressure scenarios.

### Long Snapper (LS)
Position attributes: `Snap Accuracy`, `Snap Speed`, `Blocking`

#### Snap Accuracy
- **Effect**: Precision of snaps on special teams
- **Usage**: Not yet implemented in current system
- **Range**: 0-100, where 50 is average
- **Impact**: Will affect punt and FG success when implemented

#### Snap Speed
- **Effect**: Speed of snap delivery
- **Usage**: Not yet implemented in current system
- **Range**: 0-100, where 50 is average
- **Impact**: Will affect blocked kick probability

#### Blocking
- **Effect**: Protection after snap on special teams
- **Usage**: Not yet implemented in current system
- **Range**: 0-100, where 50 is average
- **Impact**: Will contribute to special teams protection

## Staff Attributes

### Offensive Coordinator
Staff attributes: `passingOffense`, `rushingOffense`, `playCalling`

#### Passing Offense
- **Effect**: Bonus/penalty to passing play outcomes
- **Usage**: 
  - Yards bonus: `(passingOffense + playCalling - 100) / 400.0 * 3` yards
  - Applied only to pass plays, not punts or field goals
- **Range**: 0-100, where 50 is average
- **Impact**: Can add/subtract 1-3 yards per passing play

#### Rushing Offense
- **Effect**: Bonus/penalty to rushing play outcomes
- **Usage**:
  - Yards bonus: `(rushingOffense + playCalling - 100) / 400.0 * 3` yards
  - Applied only to rushing plays
- **Range**: 0-100, where 50 is average
- **Impact**: Can add/subtract 1-3 yards per rushing play

#### Play Calling
- **Effect**: Enhances both passing and rushing effectiveness
- **Usage**: Combined with specific offense ratings for situational bonuses
- **Range**: 0-100, where 50 is average
- **Impact**: Multiplier effect on coordinator effectiveness

### Defensive Coordinator
Staff attributes: `runDefense`, `passDefense`, `playCalling`

#### Run Defense (rushingDefense)
- **Effect**: Penalty to rushing play outcomes - acts as negative modifier on offensive rushing success
- **Usage**: 
  - Yards penalty: `-(rushingDefense + defensivePlayCalling - 100) / 400.0 * yardsGained` applied to rushing plays
  - Combined with playCalling for enhanced defensive effectiveness
  - Only applied in EnhancedPlaySimulator through `_applyStaffInfluence` method
- **Range**: 0-100, where 50 is average
- **Impact**: Reduces rushing yards allowed - higher values make rushing attacks less effective

#### Pass Defense (passingDefense)
- **Effect**: Penalty to passing play outcomes - acts as negative modifier on offensive passing success
- **Usage**:
  - Yards penalty: `-(passingDefense + defensivePlayCalling - 100) / 400.0 * yardsGained` applied to passing plays
  - Combined with playCalling for enhanced defensive effectiveness
  - Only applied in EnhancedPlaySimulator through `_applyStaffInfluence` method
- **Range**: 0-100, where 50 is average
- **Impact**: Reduces passing yards allowed - higher values make passing attacks less effective

#### Defensive Play Calling (defensivePlayCalling)
- **Effect**: Enhances both pass defense and run defense effectiveness
- **Usage**: Combined with specific defense ratings (passingDefense/rushingDefense) in penalty calculations
- **Range**: 0-100, where 50 is average
- **Impact**: Multiplier effect on defensive coordinator effectiveness - amplifies both pass and run defense capabilities

### Head Coach
Staff attributes: `playCalling`, `playerDevelopment`, `motivation`

#### All Head Coach Attributes
- **Effect**: Not yet implemented in play simulation
- **Usage**: Reserved for game management and development features
- **Range**: 0-100, where 50 is average
- **Impact**: Will affect timeout usage, challenges, and player growth

## Referee Attributes

### Penalty Tendencies
Referee attributes: `holdingTendency`, `passInterferenceTendency`, `roughingThePasserTendency`, `falseStartTendency`, `illegalFormationTendency`

#### All Penalty Tendencies
- **Effect**: Probability of calling specific penalty types
- **Usage**:
  - Base penalty chance: 8% per play
  - Specific penalty selection: `tendency / 100.0` probability
  - Penalty effects: Holding (-10 yards), False Start (-5 yards), etc.
- **Range**: 0-100, where 50 is average tendency
- **Impact**: Directly affects penalty frequency and game flow

## Calculation Examples

### Example 1: Pass Play
```
QB (Accuracy: 75) throws to WR (Catching: 80, Speed: 70)
Defender: CB (Coverage: 60, Speed: 65)

Base completion: 40%
QB accuracy bonus: (75-50)/100 = +25%
WR catching bonus: (80-50)/150 = +20%
Defender coverage penalty: (60-50)/150 = -6.7%
Speed differential: (70-65)/300 = +1.7%

Total completion chance: 40% + 25% + 20% - 6.7% + 1.7% = 80%
```

### Example 2: Power Run
```
RB (Rush Power: 85, Rush Speed: 60) on power run
OL run blocking average: 70
DL run defense average: 65

Base yards: (70-65)/20 = 0.25 yards
Power bonus: (85-50)/25 = 1.4 yards
Random variance: ±3 yards

Expected outcome: 0.25 + 1.4 = 1.65 yards + variance
```

### Example 3: QB Sack Avoidance
```
QB (Evasion: 85) under pressure from pass rush
Offensive line failed to provide protection

Base sack chance: 30% (when pressured)
QB evasion modifier: (85-50)/200 = -17.5%
Final sack probability: 30% - 17.5% = 12.5%

Comparison:
- Average QB (Evasion: 50): 30% sack chance
- Elite QB (Evasion: 90): 10% sack chance  
- Poor QB (Evasion: 10): 50% sack chance
```

### Example 4: Field Goal
```
Kicker (Leg Strength: 80, Accuracy: 75, Consistency: 70)
40-yard field goal

Base success rate: 85% (40+ yard kicks)
Leg strength bonus: (80-50)/200 = +15%  
Accuracy bonus: (75-50)/150 = +16.7%
Consistency bonus: (70-50)/200 = +10%

Total success rate: 85% + 15% + 16.7% + 10% = 126.7% → capped at 98%
```

### Example 5: Defensive Coordinator Impact
```
Offensive pass play gains 8 yards initially
Defensive Coordinator (Pass Defense: 90, Defensive Play Calling: 85)

DC penalty calculation: -(90 + 85 - 100) / 400.0 = -75/400 = -0.1875
Yards penalty: -0.1875 * 8 = -1.5 yards
Final yards gained: 8 - 1.5 = 6.5 yards

Comparison scenarios:
- Weak DC (Pass Defense: 30, Play Calling: 25): -(30+25-100)/400 = +0.1125 → +0.9 yards = 8.9 total
- Average DC (Pass Defense: 50, Play Calling: 50): -(50+50-100)/400 = 0 → no change = 8.0 total  
- Elite DC (Pass Defense: 95, Play Calling: 90): -(95+90-100)/400 = -0.2125 → -1.7 yards = 6.3 total
```

## Integration Points

### Player Selection
- Best players for each situation selected based on relevant attributes
- Deep passes prioritize speed + route running receivers
- Power runs prioritize power running backs
- Coverage assigns best coverage defenders

### Contextual Modifiers
- Game situation affects attribute impact (not yet implemented)
- Down and distance will modify attribute effectiveness
- Score differential and clock will influence decision making

### Realistic Variance
- Random variance prevents deterministic outcomes
- Attribute bonuses are balanced to allow upsets
- Multiple factors combine to create realistic play distributions

## Future Enhancements

### Planned Implementations
1. **Situational Modifiers**: Down/distance, score, time remaining
2. **Weather Effects**: Attribute modifications based on conditions  
3. **Fatigue System**: Attribute degradation during games
4. **Injury Impact**: Temporary attribute reductions
5. **Chemistry Bonuses**: QB-WR familiarity bonuses
6. **Scheme Fits**: Attribute bonuses for players in preferred systems

### Additional Staff Features
1. **Head Coach Game Management**: Timeout usage, challenge decisions
2. **Defensive Coordinator**: Real-time defensive adjustments
3. **Special Teams Coordinator**: ST performance bonuses
4. **Position Coaches**: Individual player development rates

This system ensures that every attribute has meaningful impact on play outcomes, creating a realistic simulation where player strengths and weaknesses directly translate to on-field performance.
