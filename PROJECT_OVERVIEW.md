# Trackmania Scrim Queue Bot - Project Overview

## What is this?
A Discord bot that manages competitive Trackmania scrim matches for the MLE (Minor League Esports) server. It handles matchmaking, player queues, map selection, check-ins, and replay file submissions.

## What does it do?
- **Queue Management**: Players join league-specific queues (Academy, Champion, Master)
- **Matchmaking**: Automatically matches 4 players when queue fills
- **Smart Map Selection**: Picks 3 maps based on least-played history
- **Check-in System**: 5-minute window to confirm participation
- **Dodge Penalties**: Escalating bans for players who don't check in
- **Replay Submission**: Sends Google Apps Script links for replay file uploads
- **Elo Tracking**: Calculates and tracks player ratings after matches
- **Sprocket Integration**: Validates players against existing Sprocket database

---

## File Structure

### Core Files
- **`src/index.ts`** - Application entry point, starts bot and database
- **`src/bot.ts`** - Discord bot initialization and event handling
- **`src/config.ts`** - Environment variable configuration

### Commands (`src/commands/`)
- **`queue.ts`** - `/queue join/leave/status/list` commands
- **`checkin.ts`** - `/checkin` command for match confirmation
- **`profile.ts`** - `/profile` command to view player stats
- **`admin.ts`** - Admin commands (ban, unban, reset queue, create match, complete scrim)

### Services (`src/services/`)
- **`queue.service.ts`** - Queue management and matchmaking logic
- **`scrim.service.ts`** - Scrim creation, activation, and completion
- **`player.service.ts`** - Player registration and Sprocket validation
- **`map.service.ts`** - Map selection based on play history
- **`ban.service.ts`** - Dodge penalty and ban management
- **`elo.service.ts`** - Elo rating calculations

### Handlers (`src/handlers/`)
- **`queueEvents.ts`** - Handles queue pop, check-in timeout, scrim activation, and replay submission notifications

### Utilities (`src/utils/`)
- **`logger.ts`** - Logging utility
- **`commandLoader.ts`** - Loads Discord slash commands
- **`urlGenerator.ts`** - Generates Google Apps Script URLs with scrim data

### Database (`src/db/`)
- **`index.ts`** - PostgreSQL connection and query utilities

### Database Schema (`db/`)
- **`schema.sql`** - Database tables (players, scrims, maps, bans, elo ratings)
- **`seed.sql`** - Sample data for testing
- **`test-seed.sql`** - Test data for automated tests

### Types (`src/types.ts`)
- TypeScript type definitions for all data models

---

## Key Workflows

1. **Queue → Match**
   - Player joins queue → Queue fills → Check-in notification sent → All check in → Replay submission link sent → Match starts

2. **Match Completion**
   - Admin marks scrim complete → Replay submission reminder sent → Elo calculated

3. **Dodge Penalty**
   - Player fails to check in → Auto-ban applied → Escalating penalties for repeat offenders

---

## Next Steps: Testing & Validation

### Critical Tests Required

#### 1. Queue System
- [ ] Test `/queue join` for each league (Academy, Champion, Master)
- [ ] Verify queue fills at 4 players and triggers matchmaking
- [ ] Test `/queue leave` removes player from queue
- [ ] Verify `/queue status` shows accurate queue counts
- [ ] Test queue priority after match cancellation

#### 2. Check-in Flow
- [ ] Verify check-in notification DM sent to all 4 players
- [ ] Test `/checkin` command marks player as checked in
- [ ] Verify 5-minute timeout triggers if not all players check in
- [ ] Test replay submission link sent after all players check in
- [ ] Verify scrim activates when all 4 players check in

#### 3. Map Selection
- [ ] Verify 3 maps selected based on least-played history
- [ ] Test map selection with insufficient map pool
- [ ] Verify map history updates after matches

#### 4. Dodge Penalties
- [ ] Test first dodge applies 5-minute ban
- [ ] Test second dodge (within 24h) applies 30-minute ban
- [ ] Test third dodge (within 24h) applies 2-hour ban
- [ ] Verify ban prevents queue joining
- [ ] Test `/admin unban` removes active bans

#### 5. Replay Submission
- [ ] Verify Google Apps Script URL sent after check-in
- [ ] Test URL includes correct scrim ID and player data
- [ ] Verify replay submission link works in browser
- [ ] Test replay reminder sent on scrim completion

#### 6. Admin Commands
- [ ] Test `/admin create-match` creates scheduled match
- [ ] Test `/admin complete-scrim` marks match complete
- [ ] Test `/admin ban` applies manual ban
- [ ] Test `/admin queue-reset` clears queues
- [ ] Verify `/admin stats` shows accurate player data

#### 7. Sprocket Integration
- [ ] Verify players validated against Sprocket database
- [ ] Test error handling for non-Sprocket users
- [ ] Verify Trackmania player profile requirement

#### 8. Elo System
- [ ] Test Elo calculation after match completion
- [ ] Verify Elo updates for winning/losing teams
- [ ] Test `/admin calc-elo` manual trigger
- [ ] Verify Elo history tracking

### Testing Environment Setup
1. Ensure test Discord server with 4+ test accounts
2. Verify Sprocket database connection (or mock for testing)
3. Confirm Google Apps Script URL is functional
4. Check database has sufficient map pool (10+ maps)

### Known Issues to Monitor
- Sprocket database connectivity
- Discord DM delivery failures
- Check-in timeout edge cases
- Concurrent queue operations
