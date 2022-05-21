<template>
<div>
  <v-container>
    <v-row>
      <v-col>
        <v-row class="table__head">
            <v-col md="6">
              <v-card-title class="table__title text-right">Sorting Training System</v-card-title>
            </v-col>
            <v-col md="3">
              <div v-if="persons.length > 0">
                Timer: <stopwatch @time="getTime" :resetTimer="resetTimer" :running="running" :resetWhenStart="true" />
              </div>
            </v-col>
            <v-col md="3">
              <div data-app>
                <TableModal @submit="generatePersons" class="table__btn--align"/>
              </div>
            </v-col>
          </v-row>
        <v-card elevation="2" outlined>
          <v-card-subtitle class="table__subtitle">{{ persons.length }} people in the list </v-card-subtitle>
          <Table :loading="loading" :persons="persons" @is-sorted="stopT" />
        </v-card>
      </v-col>
    </v-row>
  </v-container>
  <span v-if="!persons.length" class="sorting-msg"> <a>Click "Start sorting" to get started!  </a>  </span>

  <success-modal :tries="tries" :time="timeTaken" v-model="isComplete" @close="init" />
</div>
</template>

<script>
import { randomUser }  from '@/apis'
import Stopwatch from "@/components/StopWatch"
import SuccessModal from "@/components/SuccessModal.vue"

export default {
    name: "IndexPage",
    components: {
      Stopwatch, SuccessModal
    },
    data() {
      return {
        isComplete: false,
        persons: [],
        running: false,
        loading: false,
        tries: 0,
        timeTaken: null,
        resetTimer: false
      }
    },
    methods: {
      init() {
        this.isComplete = false
        this.persons = []
        this.running = false
        this.tries = 0
        this.timeTaken = null
        this.resetTimer = true
      },
      getTime(time) {
        this.timeTaken = time
      },
      stopT(isSorted, tries) {
        this.running = false
        this.isComplete = true
        this.tries = tries
      },
      startT: function() {
        this.running = true;
      },
      getRandomInt(max = 300) {
        const randomNumber = Math.floor(Math.random() * max)
        const numberExists = this.persons.some(el => el.potatoes == randomNumber)
        // console.log('randomNumber', randomNumber)
        return numberExists ? this.getRandomInt(max) : randomNumber
      },
      async generatePersons(val) {
        this.loading = true
        this.persons = []
        try {

          const users = await this.$axios.$get(randomUser + '/?results=' + val)

          if (users.results && users.results.length > 0) {
            users.results.forEach(user => {
              const person = {
                email: user.email,
                potatoes: this.getRandomInt(300),
                tags: [
                  'Customers', 'User1', 'User2'
                ],
                fullname: `${user.name.title} ${user.name.first} ${user.name.last}`,
                location: user.location.country,
                datetime: new Date(user.registered.date).toLocaleString(),
                locked: false,
              }
              this.persons.push(person)
            })

              // console.log(this.persons)
          }

          this.running = true;
        } catch(err) {
          console.log(err)
        }
          this.loading = false
      }
    }
}
</script>

<style lang="scss" scoped>
.sorting-msg {
  position: absolute;
  top: 50%;
  left: 50%;
  margin-right: -50%;
  transform: translate(-50%, -50%)
}

.table__head {
  display: flex;
  align-items: center;
  justify-content: space-between;
  .table__title {
    font-style: normal;
    font-weight: 700;
    font-size: 32px;
    line-height: 38px;
    color: #000000;
  }
  .table__btn--align {
    text-align: right;
  }
}

.table__subtitle {
  text-align: right;
  font-style: normal;
  font-weight: 700;
  font-size: 14px;
  line-height: 16px;
  color: #000000 !important;
}
</style>

<style scoped>
* {
    font-family: 'Roboto';
}
</style>