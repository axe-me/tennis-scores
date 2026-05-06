<template>
  <header class="app-header">
    <h1>
      <svg class="ball-icon" viewBox="0 0 24 24" width="20" height="20"><defs><clipPath id="bc"><circle cx="12" cy="12" r="10"/></clipPath></defs><circle cx="12" cy="12" r="10" fill="#c8e020"/><g clip-path="url(#bc)"><path d="M6 2C8.5 7 8.5 17 6 22M18 2C15.5 7 15.5 17 18 22" fill="none" stroke="#fff" stroke-width="1.5"/></g></svg>
      Tennis Scoreboard
    </h1>
    <p class="subtitle">Match Umpire Console</p>
    <div :class="['match-status', matchOver ? 'finished' : '']">
      <span class="dot"></span>
      {{ matchStatusText }}
    </div>
  </header>

  <!-- Player Name Inputs -->
  <div class="player-inputs">
    <div class="player-input-group">
      <label for="player1-name">Player 1</label>
      <input
        id="player1-name"
        v-model="player1Name"
        type="text"
        placeholder="Enter player name…"
        @focus="$event.target.select()"
      />
    </div>
    <div class="player-input-group">
      <label for="player2-name">Player 2</label>
      <input
        id="player2-name"
        v-model="player2Name"
        type="text"
        placeholder="Enter player name…"
        @focus="$event.target.select()"
      />
    </div>
  </div>
  <div class="match-title-input">
    <input
      id="match-title"
      v-model="matchTitle"
      type="text"
      placeholder="Match title (e.g. Finals — Court 1)"
      @focus="$event.target.select()"
    />
  </div>

  <!-- Match Settings -->
  <div class="control-section control-section--top">
    <div class="control-section-title">Match Settings</div>
    <div class="settings-row">
      <div class="setting-group">
        <label>Game:</label>
        <div class="toggle-group">
          <button
            :class="['toggle-btn', gameFormat === 'regular' ? 'active' : '']"
            @click="setGameFormat('regular')"
            id="btn-regular"
          >
            Regular (6)
          </button>
          <button
            :class="['toggle-btn', gameFormat === 'fast4' ? 'active' : '']"
            @click="setGameFormat('fast4')"
            id="btn-fast4"
          >
            Fast4 (4)
          </button>
          <button
            :class="['toggle-btn', gameFormat === 'proSet8' ? 'active' : '']"
            @click="setGameFormat('proSet8')"
            id="btn-proset"
          >
            Pro Set (8)
          </button>
        </div>
      </div>

      <div v-if="gameFormat !== 'proSet8'" class="setting-group">
        <label>Match:</label>
        <div class="toggle-group">
          <button
            :class="['toggle-btn', setsToWin === 2 ? 'active' : '']"
            @click="setFormat(2)"
            id="btn-bo3"
          >
            Best of 3
          </button>
          <button
            :class="['toggle-btn', setsToWin === 3 ? 'active' : '']"
            @click="setFormat(3)"
            id="btn-bo5"
          >
            Best of 5
          </button>
        </div>
      </div>

      <div class="setting-group">
        <label>Deuce:</label>
        <div class="toggle-group">
          <button
            :class="['toggle-btn', deuceMode === 'ad' ? 'active' : '']"
            @click="deuceMode = 'ad'"
            id="btn-ad"
          >
            Advantage
          </button>
          <button
            :class="['toggle-btn', deuceMode === 'noAd' ? 'active' : '']"
            @click="deuceMode = 'noAd'"
            id="btn-noad"
          >
            No-Ad
          </button>
        </div>
      </div>

      <div v-if="gameFormat !== 'proSet8'" class="setting-group">
        <label>Final Set:</label>
        <div class="toggle-group">
          <button
            :class="['toggle-btn', finalSetRule === 'tiebreak' ? 'active' : '']"
            @click="finalSetRule = 'tiebreak'"
            id="btn-tb"
          >
            Tiebreak
          </button>
          <button
            :class="['toggle-btn', finalSetRule === 'superTiebreak' ? 'active' : '']"
            @click="finalSetRule = 'superTiebreak'"
            id="btn-stb"
          >
            Super TB (10)
          </button>
          <button
            :class="['toggle-btn', finalSetRule === 'advantage' ? 'active' : '']"
            @click="finalSetRule = 'advantage'"
            id="btn-advset"
          >
            Advantage
          </button>
        </div>
      </div>

      <div class="setting-group">
        <label>First Server:</label>
        <div class="toggle-group">
          <button
            :class="['toggle-btn', server === 0 ? 'active' : '']"
            @click="server = 0"
            id="btn-serve-p1"
          >
            {{ player1Name }}
          </button>
          <button
            :class="['toggle-btn', server === 1 ? 'active' : '']"
            @click="server = 1"
            id="btn-serve-p2"
          >
            {{ player2Name }}
          </button>
        </div>
      </div>

      <div class="setting-group">
        <label>Header:</label>
        <div class="toggle-group">
          <button
            :class="['toggle-btn', showHeader ? 'active' : '']"
            @click="showHeader = !showHeader"
            id="btn-header"
          >
            {{ showHeader ? 'Shown' : 'Hidden' }}
          </button>
        </div>
      </div>

      <div class="setting-group">
        <label>Auto Save:</label>
        <div class="toggle-group">
          <button
            :class="['toggle-btn', autoSave ? 'active' : '']"
            @click="toggleAutoSave"
            id="btn-autosave"
          >
            {{ autoSave ? 'On' : 'Off' }}
          </button>
        </div>
        <span v-if="autoSave && autoSaveDirHandle" class="auto-save-status">✓ Saving to {{ autoSaveDirHandle.name }}</span>
      </div>
    </div>
  </div>

  <!-- Court Info Bar -->
  <div v-if="!matchOver" class="court-info-bar">
    <div class="court-info-item">
      <span class="court-info-label">Serve Side</span>
      <div class="side-toggle">
        <span :class="['side-box', serveSide === 'Deuce' ? 'active' : '']">Deuce</span>
        <span :class="['side-box', serveSide === 'Ad' ? 'active' : '']">Ad</span>
      </div>
    </div>
    <div v-if="shouldChangeSides" class="court-info-item changeover">
      <span class="changeover-icon">⇄</span>
      <span class="court-info-value">Change Ends</span>
    </div>
  </div>

  <!-- Scoreboard -->
  <div class="scoreboard-wrapper" ref="scoreboard">
    <table class="scoreboard">
      <thead v-if="showHeader">
        <tr>
          <th class="col-serve"></th>
          <th class="col-player col-match-title" style="text-align:left;">{{ matchTitle }}</th>
          <th
            v-for="(_, idx) in visibleSets"
            :key="'sh'+idx"
            class="col-set"
          >
            {{ isProSet ? 'Games' : 'Set ' + (idx+1) }}
          </th>
          <th v-if="!matchOver" class="col-points">Pts</th>
        </tr>
      </thead>
      <tbody>
        <!-- Player 1 row -->
        <tr :class="{ 'is-serving': server === 0 }">
          <td class="col-serve" @click="toggleServer" style="cursor:pointer;" title="Click to switch server">
            <svg v-if="server === 0" class="serve-icon" viewBox="0 0 24 24" width="16" height="16"><defs><clipPath id="sc0"><circle cx="12" cy="12" r="10"/></clipPath></defs><circle cx="12" cy="12" r="10" fill="#c8e020"/><g clip-path="url(#sc0)"><path d="M6 2C8.5 7 8.5 17 6 22M18 2C15.5 7 15.5 17 18 22" fill="none" stroke="#fff" stroke-width="1.5"/></g></svg>
          </td>
          <td class="col-player">
            <div class="player-name">
              {{ displayName(0) }}
              <span v-if="matchOver && matchWinner === 0" class="winner-badge">WIN</span>
            </div>
          </td>
          <td
            v-for="(_, idx) in visibleSets"
            :key="'s1'+idx"
            class="col-set"
          >
            <div :class="setScoreClass(0, idx)">
              {{ sets[idx] ? sets[idx][0] : 0 }}
              <span v-if="sets[idx] && sets[idx].tiebreakPlayed && (idx < currentSetIndex || matchOver) && sets[idx][0] < sets[idx][1]" class="tiebreak-indicator">
                {{ sets[idx].tiebreakLoserPoints }}
              </span>
            </div>
          </td>
          <td v-if="!matchOver" class="col-points">
            <div :class="['points-score', pointHighlight(0)]">
              {{ displayPoints(0) }}
            </div>
          </td>
        </tr>
        <!-- Player 2 row -->
        <tr :class="{ 'is-serving': server === 1 }">
          <td class="col-serve" @click="toggleServer" style="cursor:pointer;" title="Click to switch server">
            <svg v-if="server === 1" class="serve-icon" viewBox="0 0 24 24" width="16" height="16"><defs><clipPath id="sc1"><circle cx="12" cy="12" r="10"/></clipPath></defs><circle cx="12" cy="12" r="10" fill="#c8e020"/><g clip-path="url(#sc1)"><path d="M6 2C8.5 7 8.5 17 6 22M18 2C15.5 7 15.5 17 18 22" fill="none" stroke="#fff" stroke-width="1.5"/></g></svg>
          </td>
          <td class="col-player">
            <div class="player-name">
              {{ displayName(1) }}
              <span v-if="matchOver && matchWinner === 1" class="winner-badge">WIN</span>
            </div>
          </td>
          <td
            v-for="(_, idx) in visibleSets"
            :key="'s2'+idx"
            class="col-set"
          >
            <div :class="setScoreClass(1, idx)">
              {{ sets[idx] ? sets[idx][1] : 0 }}
              <span v-if="sets[idx] && sets[idx].tiebreakPlayed && (idx < currentSetIndex || matchOver) && sets[idx][1] < sets[idx][0]" class="tiebreak-indicator">
                {{ sets[idx].tiebreakLoserPoints }}
              </span>
            </div>
          </td>
          <td v-if="!matchOver" class="col-points">
            <div :class="['points-score', pointHighlight(1)]">
              {{ displayPoints(1) }}
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <!-- Match winner banner -->
  <div v-if="matchOver" class="match-over-banner">
    <div class="trophy">🏆</div>
    <div class="winner-text">{{ displayName(matchWinner) }} wins the match!</div>
    <div class="match-score-text">{{ matchScoreSummary }}</div>
  </div>

  <!-- Controls -->
  <div class="controls-panel">
    <!-- Score Buttons -->
    <div class="control-section">
      <div class="control-section-title">Score Point</div>
      <div class="score-buttons">
        <button class="score-btn" @click="addPoint(0)" :disabled="matchOver" id="btn-score-p1">
          <span class="btn-icon">＋</span>
          <span class="btn-label">{{ displayName(0) }}</span>
        </button>
        <button class="score-btn" @click="addPoint(1)" :disabled="matchOver" id="btn-score-p2">
          <span class="btn-icon">＋</span>
          <span class="btn-label">{{ displayName(1) }}</span>
        </button>
      </div>
    </div>

    <!-- Actions -->
    <div class="control-section">
      <div class="control-section-title">Actions</div>
      <div class="action-buttons">
        <button class="action-btn" @click="undoPoint" :disabled="history.length === 0" id="btn-undo">
          ↩ Undo Point
        </button>
        <button class="action-btn" @click="saveScreenshot" id="btn-screenshot">
          📷 Save Screenshot
        </button>
        <button class="action-btn danger" @click="resetMatch" id="btn-reset">
          ✕ Reset Match
        </button>
      </div>
    </div>
  </div>

  <!-- History -->
  <div class="history-panel" v-if="history.length > 0">
    <button class="history-toggle" @click="showHistory = !showHistory">
      {{ showHistory ? '▾' : '▸' }} Point History ({{ history.length }})
    </button>
    <div v-if="showHistory" class="history-list">
      <div
        v-for="(entry, idx) in [...history].reverse()"
        :key="idx"
        class="history-item"
      >
        #{{ history.length - idx }} — {{ entry }}
      </div>
    </div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas'

const POINT_NAMES = ['0', '15', '30', '40']

export default {
  name: 'TennisScoreboard',

  data() {
    return {
      player1Name: 'Player 1',
      player2Name: 'Player 2',
      matchTitle: '',
      showHeader: false,
      autoSave: false,
      autoSaveDirHandle: null,
      server: 0, // 0 = player 1, 1 = player 2
      gameFormat: 'regular', // 'regular' (6), 'fast4' (4), 'proSet8' (8 games, 1 set)
      setsToWin: 2, // Best of 3
      deuceMode: 'ad', // 'ad' or 'noAd'
      finalSetRule: 'advantage', // 'tiebreak', 'superTiebreak', 'advantage'

      // Points in current game: [p1, p2]
      points: [0, 0],

      // Sets: array of set objects, each is [p1Games, p2Games, ...meta]
      sets: [],
      currentSetIndex: 0,

      // Tiebreak state
      isTiebreak: false,
      tiebreakPoints: [0, 0],

      // Match state
      matchOver: false,
      matchWinner: null,

      // History for undo
      history: [],
      stateHistory: [],

      showHistory: false,
    }
  },

  computed: {
    maxSets() {
      if (this.isProSet) return 1
      return this.setsToWin * 2 - 1
    },

    visibleSets() {
      return this.currentSetIndex + 1
    },

    completedSets() {
      return this.sets.filter((s, i) => i < this.currentSetIndex).length
    },

    isProSet() {
      return this.gameFormat === 'proSet8'
    },

    gamesNeeded() {
      if (this.gameFormat === 'fast4') return 4
      if (this.gameFormat === 'proSet8') return 8
      return 6
    },

    setsWon() {
      let p1 = 0, p2 = 0
      for (let i = 0; i < this.currentSetIndex; i++) {
        const s = this.sets[i]
        if (s && s[0] > s[1]) p1++
        else if (s && s[1] > s[0]) p2++
      }
      return [p1, p2]
    },

    matchStatusText() {
      if (this.matchOver) return 'Match Complete'
      if (this.isTiebreak) {
        if (this.isFinalSet && this.finalSetRule === 'superTiebreak') return 'Super Tiebreak (10)'
        if (this.isFinalSet && this.finalSetRule === 'tiebreak') return 'Match Tiebreak'
        return 'Tiebreak'
      }
      if (this.points[0] === 0 && this.points[1] === 0 && this.currentGames[0] === 0 && this.currentGames[1] === 0 && this.currentSetIndex === 0) {
        return 'Ready to Start'
      }
      return 'In Progress'
    },

    currentGames() {
      const s = this.sets[this.currentSetIndex]
      return s ? [s[0], s[1]] : [0, 0]
    },

    isFinalSet() {
      return this.setsWon[0] === this.setsToWin - 1 && this.setsWon[1] === this.setsToWin - 1
    },

    matchScoreSummary() {
      const parts = []
      for (let i = 0; i <= this.currentSetIndex && i < this.sets.length; i++) {
        const s = this.sets[i]
        let str = `${s[this.matchWinner]}-${s[1 - this.matchWinner]}`
        if (s.tiebreakPlayed) {
          str += `(${s.tiebreakLoserPoints})`
        }
        parts.push(str)
      }
      return parts.join('  ')
    },

    matchInProgress() {
      return this.currentSetIndex > 0 || this.points[0] > 0 || this.points[1] > 0 || this.currentGames[0] > 0 || this.currentGames[1] > 0
    },

    totalPointsInGame() {
      if (this.isTiebreak) {
        return this.tiebreakPoints[0] + this.tiebreakPoints[1]
      }
      return this.points[0] + this.points[1]
    },

    serveSide() {
      // Even total points → Deuce (right), Odd → Ad (left)
      return this.totalPointsInGame % 2 === 0 ? 'Deuce' : 'Ad'
    },

    shouldChangeSides() {
      if (this.matchOver) return false
      const totalGames = this.currentGames[0] + this.currentGames[1]

      // In a tiebreak, change ends every 6 points
      if (this.isTiebreak) {
        const totalPts = this.tiebreakPoints[0] + this.tiebreakPoints[1]
        return totalPts > 0 && totalPts % 6 === 0
      }

      // Change ends after odd total games, only at start of new game
      if (totalGames > 0 && totalGames % 2 === 1 && this.points[0] === 0 && this.points[1] === 0) {
        return true
      }

      return false
    },
  },

  created() {
    this.initSets()
  },

  methods: {
    initSets() {
      this.sets = []
      for (let i = 0; i < this.maxSets; i++) {
        const setObj = [0, 0]
        setObj.tiebreakPlayed = false
        setObj.tiebreakLoserPoints = 0
        this.sets.push(setObj)
      }
    },

    displayName(playerIdx) {
      return playerIdx === 0 ? this.player1Name : this.player2Name
    },

    toggleServer() {
      this.server = this.server === 0 ? 1 : 0
    },

    displayPoints(playerIdx) {
      if (this.matchOver) return ''
      if (this.isTiebreak) {
        return this.tiebreakPoints[playerIdx]
      }
      const p1 = this.points[0]
      const p2 = this.points[1]
      if (this.deuceMode === 'ad') {
        if (p1 >= 3 && p2 >= 3) {
          if (p1 === p2) return playerIdx === 0 ? '40' : '40'
          if (p1 > p2) return playerIdx === 0 ? 'AD' : '40'
          return playerIdx === 0 ? '40' : 'AD'
        }
      }
      const pts = this.points[playerIdx]
      return pts <= 3 ? POINT_NAMES[pts] : '40'
    },

    pointHighlight(playerIdx) {
      if (this.matchOver) return ''
      if (this.isTiebreak) return ''
      const display = this.displayPoints(playerIdx)
      if (display === 'AD') return 'highlight'
      return ''
    },

    setScoreClass(playerIdx, setIdx) {
      const classes = ['set-score']
      if (setIdx === this.currentSetIndex && !this.matchOver) {
        classes.push('current')
      } else if (setIdx < this.currentSetIndex) {
        const s = this.sets[setIdx]
        if (s[playerIdx] > s[1 - playerIdx]) {
          classes.push('won')
        } else {
          classes.push('lost')
        }
      }
      return classes.join(' ')
    },

    setGameFormat(format) {
      if (this.matchInProgress) {
        if (!confirm('Changing format will reset the match. Continue?')) return
      }
      this.gameFormat = format
      if (format === 'proSet8') {
        this.setsToWin = 1
      } else if (this.setsToWin < 2) {
        this.setsToWin = 2
      }
      this.resetMatch()
    },

    setFormat(setsToWin) {
      if (this.matchInProgress) {
        if (!confirm('Changing format will reset the match. Continue?')) return
      }
      this.setsToWin = setsToWin
      this.resetMatch()
    },

    saveState() {
      this.stateHistory.push({
        points: [...this.points],
        sets: this.sets.map(s => {
          const copy = [s[0], s[1]]
          copy.tiebreakPlayed = s.tiebreakPlayed
          copy.tiebreakLoserPoints = s.tiebreakLoserPoints
          return copy
        }),
        currentSetIndex: this.currentSetIndex,
        isTiebreak: this.isTiebreak,
        tiebreakPoints: [...this.tiebreakPoints],
        server: this.server,
        matchOver: this.matchOver,
        matchWinner: this.matchWinner,
      })
    },

    addPoint(playerIdx) {
      if (this.matchOver) return
      this.saveState()

      if (this.isTiebreak) {
        this.addTiebreakPoint(playerIdx)
      } else {
        this.addGamePoint(playerIdx)
      }

      if (this.autoSave) {
        this.$nextTick(() => this.autoSaveScreenshot())
      }
    },

    addGamePoint(playerIdx) {
      const other = 1 - playerIdx
      this.points[playerIdx]++

      const p = this.points[playerIdx]
      const o = this.points[other]

      let gameWon = false

      if (this.deuceMode === 'noAd') {
        // No advantage: first to 4 wins, at deuce sudden death
        if (p >= 4 && p > o) {
          gameWon = true
        }
      } else {
        // Standard advantage
        if (p >= 4 && p - o >= 2) {
          gameWon = true
        }
      }

      if (gameWon) {
        this.history.push(`${this.displayName(playerIdx)} wins game → ${this.currentGames[0] + (playerIdx === 0 ? 1 : 0)}-${this.currentGames[1] + (playerIdx === 1 ? 1 : 0)} Set ${this.currentSetIndex + 1}`)
        this.winGame(playerIdx)
      } else {
        const ptsDisplay = this.displayPoints(0) + '-' + this.displayPoints(1)
        this.history.push(`Point ${this.displayName(playerIdx)} → ${ptsDisplay}`)
      }
    },


    winGame(playerIdx) {
      const set = this.sets[this.currentSetIndex]
      set[playerIdx]++
      this.points = [0, 0]

      const p = set[playerIdx]
      const o = set[1 - playerIdx]
      const needed = this.gamesNeeded
      const isFast4 = this.gameFormat === 'fast4'

      let setWon = false

      if (isFast4) {
        // Fast4: first to 4 games wins, tiebreak at 3-3
        if (p === needed && o < needed) {
          setWon = true
        } else if (p === needed - 1 && o === needed - 1) {
          // 3-3 → tiebreak
          this.startTiebreak()
          return
        }
      } else {
        // Regular / Pro Set: need 2-game margin, tiebreak at needed-all
        if (p >= needed && p - o >= 2) {
          setWon = true
        } else if (p === needed + 1 && o === needed) {
          // Won via tiebreak result
          setWon = true
        } else if (p === needed && o === needed) {
          // Tiebreak trigger
          if (this.isProSet) {
            this.startTiebreak()
            return
          } else if (this.isFinalSet && this.finalSetRule === 'advantage') {
            // No tiebreak in advantage final set, play on
          } else {
            this.startTiebreak()
            return
          }
        }
      }

      if (setWon) {
        this.winSet(playerIdx)
      } else {
        // Switch server
        this.server = 1 - this.server
      }
    },

    startTiebreak() {
      this.isTiebreak = true
      this.tiebreakPoints = [0, 0]
    },

    addTiebreakPoint(playerIdx) {
      const other = 1 - playerIdx
      this.tiebreakPoints[playerIdx]++

      const p = this.tiebreakPoints[playerIdx]
      const o = this.tiebreakPoints[other]

      // Determine tiebreak target
      let target = 7
      if (this.isFinalSet && this.finalSetRule === 'superTiebreak') {
        target = 10
      }

      // Switch server every 2 points (after first point)
      const totalPoints = p + o
      if (totalPoints === 1 || (totalPoints > 1 && (totalPoints - 1) % 2 === 0)) {
        this.server = 1 - this.server
      }

      if (p >= target && p - o >= 2) {
        const idx = this.currentSetIndex
        const set = this.sets[idx]
        const newSet = [set[0], set[1]]
        newSet[playerIdx] = newSet[playerIdx] + 1
        newSet.tiebreakPlayed = true
        newSet.tiebreakLoserPoints = o
        this.sets.splice(idx, 1, newSet)

        this.history.push(`${this.displayName(playerIdx)} wins tiebreak ${p}-${o} → Set ${idx + 1}`)
        this.isTiebreak = false
        this.tiebreakPoints = [0, 0]
        this.points = [0, 0]
        this.winSet(playerIdx)
      } else {
        this.history.push(`TB point ${this.displayName(playerIdx)} → ${this.tiebreakPoints[0]}-${this.tiebreakPoints[1]}`)
      }
    },

    winSet(playerIdx) {
      // Count sets won
      let p1Sets = 0, p2Sets = 0
      for (let i = 0; i <= this.currentSetIndex; i++) {
        const s = this.sets[i]
        if (s[0] > s[1]) p1Sets++
        else if (s[1] > s[0]) p2Sets++
      }

      const setsForPlayer = playerIdx === 0 ? p1Sets : p2Sets

      if (setsForPlayer >= this.setsToWin) {
        this.matchOver = true
        this.matchWinner = playerIdx
        this.history.push(`🏆 ${this.displayName(playerIdx)} wins the match!`)
      } else {
        this.currentSetIndex++
        this.server = 1 - this.server

        // If entering the final set and rule is tiebreak/superTiebreak,
        // the entire deciding set is played as a single tiebreak
        if (this.isFinalSet && (this.finalSetRule === 'tiebreak' || this.finalSetRule === 'superTiebreak')) {
          this.startTiebreak()
        }
      }
    },

    undoPoint() {
      if (this.stateHistory.length === 0) return
      const prev = this.stateHistory.pop()
      this.points = prev.points
      this.sets = prev.sets
      this.currentSetIndex = prev.currentSetIndex
      this.isTiebreak = prev.isTiebreak
      this.tiebreakPoints = prev.tiebreakPoints
      this.server = prev.server
      this.matchOver = prev.matchOver
      this.matchWinner = prev.matchWinner
      this.history.pop()

      if (this.autoSave) {
        this.$nextTick(() => this.autoSaveScreenshot())
      }
    },

    resetMatch() {
      this.points = [0, 0]
      this.currentSetIndex = 0
      this.isTiebreak = false
      this.tiebreakPoints = [0, 0]
      this.matchOver = false
      this.matchWinner = null
      this.history = []
      this.stateHistory = []
      this.initSets()
    },

    async saveScreenshot() {
      try {
        const el = this.$refs.scoreboard
        const canvas = await html2canvas(el, {
          backgroundColor: null,
          scale: 2,
        })

        // Try File System Access API for save-as dialog
        if (window.showSaveFilePicker) {
          const handle = await window.showSaveFilePicker({
            suggestedName: `scoreboard-${Date.now()}.png`,
            types: [{
              description: 'PNG Image',
              accept: { 'image/png': ['.png'] },
            }],
          })
          const writable = await handle.createWritable()
          const blob = await new Promise(resolve => canvas.toBlob(resolve, 'image/png'))
          await writable.write(blob)
          await writable.close()
        } else {
          // Fallback: direct download
          const link = document.createElement('a')
          link.download = `scoreboard-${Date.now()}.png`
          link.href = canvas.toDataURL('image/png')
          link.click()
        }
      } catch (err) {
        if (err.name !== 'AbortError') {
          console.error('Screenshot failed:', err)
        }
      }
    },

    async toggleAutoSave() {
      if (this.autoSave) {
        // Turn off
        this.autoSave = false
        this.autoSaveDirHandle = null
        return
      }

      // Turn on — pick a folder
      if (!window.showDirectoryPicker) {
        alert('Your browser does not support folder selection. Use Chrome or Edge.')
        return
      }

      try {
        this.autoSaveDirHandle = await window.showDirectoryPicker({ mode: 'readwrite' })
        this.autoSave = true
      } catch (err) {
        if (err.name !== 'AbortError') {
          console.error('Folder picker failed:', err)
        }
      }
    },

    async autoSaveScreenshot() {
      if (!this.autoSaveDirHandle) return
      try {
        const el = this.$refs.scoreboard
        const canvas = await html2canvas(el, {
          backgroundColor: null,
          scale: 2,
        })
        const blob = await new Promise(resolve => canvas.toBlob(resolve, 'image/png'))
        const fileName = `scoreboard-${Date.now()}.png`
        const fileHandle = await this.autoSaveDirHandle.getFileHandle(fileName, { create: true })
        const writable = await fileHandle.createWritable()
        await writable.write(blob)
        await writable.close()
      } catch (err) {
        console.error('Auto-save failed:', err)
        this.autoSave = false
        this.autoSaveDirHandle = null
      }
    },
  },
}
</script>
