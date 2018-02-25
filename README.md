### Baseball JSON Schema

This repository contains a JSON schema for representing pitch-by-pitch and play-by-play events in baseball games.

To validate an instance document using [ajv](https://epoberezkin.github.io/ajv/):

```
$ ajv -s schema/baseball-schema-1.0.0.json -d examples/NYA201508040.json
examples/NYA201508040.json valid
$
```

The schema validates the following structure.  Much of this structure is borrowed from the [Project Retrosheet](http://www.retrosheet.org) [Event File](http://www.retrosheet.org/eventfile.htm) format.

```
description (string, 1:1, a brief description of the game)
source (string, 1:1, a brief description of the source of the game data)
games (array)
  game (object, 1:n, the core game structure)
    game_id (string, 1:1, unique identifier for the game)
    visitor_team (object, 1:1, structure for the visiting team)
      team_id (string, 1:1, unique identifier for the team)
      team_name (string, 0:1, descriptive name of the team)
      lineup (array)
        player (object, 1:n, player structure)
          player_id (string, 1:1, unique identifier for player)
          player_first_name (string, 0:1, first name of player)
          player_last_name (string, 0:1, last name of player)
          bats (enum = L|R|B, 0:1, batting handedness of player)
          throws (enum = L|R, 0:1, throwing handedness of player)
        starter (boolean, 1:1, whether the player is in the starting lineup)
        lineup_position (integer, 0:1, player's position in the lineup, not present for non-starters)
        fielder_position (integer, 0:1, player's position in the field, using traditional baseball numeric indicators for each position and 10 for DH, not present for non-starters)
    home_team (object, 1:1, structure for home team, same as visitor_team above)
    site (object, 0:1, structure for location of game)
      site_id (string, 0:1, unique identifier for site)
      site_name (string, 0:1, descriptive name for site)
    start_date (date, 0:1, date on which the game began)
    start_time (time, 0:1, time at which the game began)
    daynight (enum = day|night, 0:1, whether the game was officially a "day" or "night" game)
    use_dh (boolean, 0:1, whether the game uses the designated hitter)
    ump_home (object, 0:1, info about the home plate umpire)
      umpire_id (string, 1:1, unique identifier for umpire)
      umpire_last_name (string, 0:1, first name of umpire)
      umpire_first_name (string, 0:1, last name of umpire)
    ump_1b (object, 0:1, info about the first base umpire, same as ump_home above)
    ump_2b (object, 0:1, info about the second base umpire, same as ump_home above)
    ump_3b (object, 0:1, info about the third base umpire, same as ump_home above)
    ump_lf (object, 0:1, info about the left field line umpire, same as ump_home above)
    ump_rf (object, 0:1, info about the right field line umpire, same as ump_home above)
    ump_field (object, 0:1, info about the field umpire (e.g., in a two-umpire crew), same as ump_home above)
    how_scored (string, 0:1, description of how the game was scored)
    pitches (string, 0:1, whether the data includes pitch-by-pitch data)
    temp (string, 0:1, description of the game time temperature)
    wind (string, 0:1, description of the game time wind direction)
    wind_speed (string, 0:1, description of the game time wind speed)
    field_conditions (string, 0:1, description of the field conditions)
    precip (string, 0:1, description of any precipitation)
    sky (string, 0:1, description of sky conditions)
    end_time (time, 0:1, end time of game)
    attendance (integer, attendance)
    winning_pitcher (string, 0:1, player ID of the winning pitcher)
    losing_pitcher (string, 0:1, player ID of the losing pitcher)
    save_pitcher (string, 0:1, player ID of pitcher awarded the save)
    plays (array, contains the record of plays that occured in the game, in chronological order)

      play (object, 1:n, the core play structure...note that there are four types of plays)

      type (constant string ="play", represents a basic baseball play)
      inning (integer, 1:1, inning)
      batting_team_id (string, 1:1, team ID of the team at bat during the play)
      batting_player_id (string, 1:1, player ID of the player at bat during the play)
      count (string, 0:1, two-character string representing the count when the play occurred, where the first character is number of balls and second character is number of strikes)
      pitch_sequence (string, 0:1, representation of the sequence of pitches, according to the Retrosheet convention)
      play (string, 0:1, representation of the play, according to the Retrosheet convention)
      enhanced_pitch_sequence (object, 0:1, enhanced pitch-by-pitch information)
        type (string, 0:1, Retrosheet code for type of pitch)
        velocity (number, 0:1, velocity of pitch)
        pitch_type (string, 0:1, code/description for type of pitch (i.e., fastball, curveball, etc.))
        location: either a string description of the location, or the following object structure:
          x (number, 0:1, representation of the horizontal location of the pitch)
          z (number, 0:1, representation of the vertical location of the pitch)

      type (constant string ="substitution", represents a player substitution in the lineup)
      substitution (object, 1:1, structure representing the new player)
        player (object, 1:1, player information)
          player_id (string, 1:1, unique identifier of new player)
          player_first_name (string, 0:1, first name of new player)
          player_last_name (string, 0:1, last name of new player)
        lineup_position (integer, 1:1, player's position in the lineup)
        fielder_position (integer, 1:1, player's position in the field, with 11=pinch hitter and 12=pinch runner)

      type (constant string ="comment", represents a comment)
      comment (string, 1:1, the comment)

      type (constant string ="batting_adjustment", represents a rare situation where a player bats from a side inconsistent with how he/she is identified in the lineup)
      batting_player_id (string, 1:1, unique identifier of the batter)
      hand (enum=L|R, 1:1, the handedness)

      type (constant string ="pitching_adjustment", represents a rare situation where a player pitches from a side inconsistent with how he/she is identified in the lineup)
      pitching_player_id (string, 1:1, unique identifier of the pitcher)
      hand (enum=L|R, 1:1, the handedness)

      type (constant string =replay, represents a replay review)
      inning (integer, 1:1, the inning in which the review occurred)
      batting_team_id (string, 1:1, unique identifier of the batting team when the review occurred)
      batting_player_id (string, 0:1, unique identifier of the player at bat when the review occurred)
      umpire_id (string, 0:1, unique identifier of the umpire supervising the review)
      site_id (string, 0:1, unique identifier of the site where the review occurred)
      reason (string, 0:1, code or description of the reason for the review)
      reversed (boolean, 0:1, whether the review resulted in reversal of the call on the field)
      team (string, 0:1, unique identifier of the team that initiated the review with a challenge)
      replay_type (string, 0:1, code or description of the type of review)

```
