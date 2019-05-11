<template>
  <div class="home-view">
    <section class="row">
      <div class="small-6 columns you">
        <h1 class="text-center"> You </h1>
        <div class="nenoeil">
          <img src="https://images.ecosia.org/suIhwIV-HDh3wN6pOPhccC0rzOY=/0x390/smart/http%3A%2F%2Fvignette3.wikia.nocookie.net%2Fpixar%2Fimages%2F0%2F03%2FMike.png%2Frevision%2Flatest%3Fcb%3D20110416121536" alt="">
        </div>

        <div class="healthbar">
          <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;" :style="{width: playerHealth + '%'}">
            {{ playerHealth }}
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center"> Monster </h1>
        <img src="https://images.ecosia.org/wZ9681ch7vIsWxvvpP6MnfyeG8U=/0x390/smart/http%3A%2F%2Fvignette1.wikia.nocookie.net%2Fvillains%2Fimages%2F0%2F09%2FRandall_Boggs.png%2Frevision%2Flatest%3Fcb%3D20140109002054" alt="">
        <div class="healthbar">
          <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;" :style="{width: monsterHealth + '%'}">
            {{ monsterHealth }}
          </div>
        </div>
      </div>
    </section>

    <section class="row controls" v-if="!gameIsRunning">
      <div class="small-12 columns button-game">
        <button id="start-game" class=" primary" @click="startGame">START GAME</button>
      </div>
    </section>

    <section class="row controls " v-else>
      <div class="small-12 columns button-game">
        <button id="attack" class="alert" @click="attack">ATTACK</button>
        <button id="special-attack" clas="warning" @click="specialAttack">SPECIAL ATTACK</button>
        <button id="heal" class="success" @click="heal">HEAL</button>
        <button id="give-up" class="secondary" @click="giveUp">SURRENDER</button>
      </div>
    </section>

    <section class="row log" v-if="turns.length > 0">
      <div class="small-12 columns">
        <ul>
          <li v-for="turn in turns" :key="turn.id" :class="{'player-turn': turn.isPlayer,'monster-turn': !turn.isPlayer}" class="text-center">
            {{ turn.text }}
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>

export default {
  name: 'HomeView',
  data () {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      gameIsRunning: false,
      turns: []
    }
  },
  components: {},
  computed: {
  },
  created () {},
  methods: {
    startGame () {
      this.gameIsRunning = true
      this.playerHealth = 100
      this.monsterHealth = 100
      this.turns = []
    },
    attack () {
      let damage = this.calculateDamage(10, 3)
      this.monsterHealth -= damage
      this.turns.unshift({
        isPlayer: true,
        text: 'Vous avez blessé le monstre pour ' + damage + ' pv'
      })
      if (this.checkWin()) {
        return
      }
      this.monsterAttacks()
    },
    specialAttack () {
      let damage = this.calculateDamage(13, 6)
      this.monsterHealth -= damage
      this.turns.unshift({
        isPlayer: true,
        text: 'Vous avez massacrer le monstre pour ' + damage + ' pv'
      })
      if (this.checkWin()) {
        return
      }
      console.log(damage)
      this.monsterAttacks()
    },
    heal () {
      if (this.playerHealth <= 90) {
        this.playerHealth += 10
      } else {
        this.playerHealth = 100
      }
      this.turns.unshift({
        isPlayer: true,
        text: 'Vous avez régénérer 10 pv'
      })
      this.monsterAttacks()
    },
    giveUp () {
      this.gameIsRunning = false
      this.turns = []
    },
    monsterAttacks () {
      let damage = this.calculateDamage(12, 5)
      this.playerHealth -= damage
      this.checkWin()
      this.turns.unshift({
        isPlayer: false,
        text: 'Le monstre vous a blessé pour ' + damage + ' pv'
      })
    },
    calculateDamage (max, min) {
      return Math.max(Math.floor(Math.random() * max) + 1, min)
    },
    checkWin () {
      if (this.monsterHealth <= 0) {
        if (confirm('tu as gagné !! Nouvelle partie ?')) {
          this.startGame()
        } else {
          this.gameIsRunning = false
        }
        return true
      } else if (this.playerHealth <= 0) {
        if (confirm('tu as perdu !! Nouvelle partie ?')) {
          this.startGame()
        } else {
          this.gameIsRunning = false
        }
        return true
      }
      return false
    }
  }
}
</script>
<style lang="scss">
.home-view{
  padding-top: 5rem;
}
.text-center{
  text-align: center;
}
.nenoeil{
  width: 72%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.you{
 width: 50%;
 height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.healthbar{
  width: 80%;
  height: 4rem;
  background-color: #eee;
  margin:auto;
  transition: width .5s;
  display: flex;
  align-items: center;
  justify-content: center;
}
.button-game{
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 2rem;
  padding: 2rem 0;
  border: 1px solid lightgray;
  box-shadow: 5px 10px 8px #888888;
  button{
    margin-left: 2rem;
  }
}
.log{
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 2rem;
  padding: 2rem 0;
  border: 1px solid lightgray;
  box-shadow: 5px 10px 8px #888888;
  ul{
    list-style: none;
    .player-turn{
      color: blue;
      background-color: #e4e8ff;
      text-transform: uppercase;

    }
    .monster-turn{
      color: red;
      background-color: #ffc0c1;
      text-transform: uppercase;
      padding: 5px;
    }
  }
}

</style>
