<template>
  <div>
    <v-row justify="center" align="start">
      <v-col>
        <v-card>
          <v-card-text>
            <v-row justify="center" align="center" v-for="proposition in propositions" :key="proposition">
              <template v-for="lettre in proposition" :keys="lettre">
              
                <v-sheet
                  align="center"
                  v-if="lettre.result === 1"
                  color="yellow darken-2"
                  elevation="1"
                  class="mt-1 ml-1 font-weight-black text-h4"
                  height="50"
                  width="50"
                >{{lettre.lettre}}</v-sheet>
                <v-sheet
                  align="center"
                  v-else-if="lettre.result === 2"
                  color="green darken-2"
                  elevation="1"
                  class="mt-1 ml-1 font-weight-black text-h4"
                  height="50"
                  width="50"
                >{{lettre.lettre}}</v-sheet>
                <v-sheet
                  v-else
                  align="center"
                  color="grey darken-2"
                  elevation="1"
                  class="mt-1 ml-1 font-weight-black text-h4"
                  height="50"
                  width="50"
                >{{lettre.lettre}}</v-sheet>
              
              </template>
            </v-row>
          </v-card-text>
          <v-card-actions align="center">
            <template v-if="game_status === 'start'"> 
              <v-text-field
                v-model="current_proposition"
                label="Proposition"
              ></v-text-field>

              <v-spacer />
              <v-btn
                color="primary"
                v-on:click="proposition"
              >
                Soumettre
              </v-btn>
            </template>
            <template v-else-if="wait">
               <v-btn
                color="primary"
                v-on:click="newWord"
              >
                Nouveau mots
              </v-btn>
            </template>
            <template v-else-if="finish">
               <v-btn
                color="primary"
                v-on:click="newWord"
              >
                Nouvelle partie
              </v-btn>
            </template>
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col>
        <v-card>
          <v-card-text>
            <v-row justify="center" align="center" class="pt-5 pl-4 pr-4 pb-5">
              {{ score }} / 50
              <v-progress-linear 
                    color="light-blue"
                    height="10"
                    :value="score * 2"
                    striped
              ></v-progress-linear>
            </v-row>
            <v-list dense>
              <v-subheader>Historique</v-subheader>
              <v-list-item-group
                color="primary"
              >
                <v-list-item
                  v-for="(word, index) in historique" 
                  :key="index"
                >
                  <v-list-item-icon class='mt-0'>
                    <v-avatar
                      color="primary"
                      size="38"
                    >{{ word.score }} </v-avatar>
                  </v-list-item-icon>
                  <v-list-item-content>
                    <v-list-item-title v-text="word.word"></v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
              </v-list-item-group>
            </v-list>
        </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data () {
    return {
      current_proposition: '',
      index_current_proposition: 0,
      secret_word: '',
      secret_words : [
        'ABACA',
        'ABALE',
        'ABATS',
        'ABBES',
        'ABCES',
        'ABDOS',
        'ABEES',
        'ABERS',
        'ABETI',
        'ABIES',
        'ABIMA',
        'ABIME',
        'ABLES',
        'ABOIE',
        'ABOIS',
        'ABOLI'
      ],
      game_status : "wait",
      message: '',
      score : 0,
      propositions: [ 
        [{}, {}, {}, {}, {}],
        [{}, {}, {}, {}, {}],
        [{}, {}, {}, {}, {}],
        [{}, {}, {}, {}, {}],
        [{}, {}, {}, {}, {}]
      ],
      historique: [], 
    };
  },
  methods: {
    proposition () {
      this.current_proposition = this.current_proposition.toUpperCase()
      if (this.current_proposition.length === 5 ){

        if ( this.index_current_proposition < 5 ){

          const object_current_proposition = []
          for (var i = 0; i <= this.current_proposition.length-1; i++) {
            object_current_proposition.push(
              {
                lettre: this.current_proposition[i],
                result: 0
              }
            )
          } 
          this.propositions[this.index_current_proposition] = object_current_proposition

          if (this.current_proposition === this.secret_word){
            for (var i = 0; i <= object_current_proposition.length-1; i++) {
              object_current_proposition[i].result = 2
            } 
            this.win()
          } else {
            for (var i = 0; i <= object_current_proposition.length-1; i++) {
              var indexOfLettre = this.secret_word.indexOf(object_current_proposition[i].lettre);
              if (indexOfLettre >= 0) {
                object_current_proposition[i].result += 1
                if (indexOfLettre === i) {
                  object_current_proposition[i].result += 1
                }
              } 
            }
          }
          this.current_proposition = ''
          
          if ( this.index_current_proposition === 5 && this.current_proposition !== this.secret_word){
            this.lose()
          }
          this.index_current_proposition = this.index_current_proposition+1;
        } else {
          this.lose()
        }
      }
    },
    win(){
      this.score = this.score + (5-this.index_current_proposition)
      this.historique.push( 
        {
          score: (5-this.index_current_proposition),
          word: this.current_proposition
        } 
      )
      this.current_proposition = ''
      this.game_status = 'wait'
      if ( this.score === 50 ){
        this.finish()
      }
    },
    lose(){
      this.current_proposition = ''

      this.historique.push( 
        {
          score: 0,
          word: this.secret_word
        } 
      )
      this.game_status = 'wait'
    },
    newWord(){
      this.secret_word = this.secret_words[Math.floor(Math.random() * this.secret_words.length)]
      this.current_proposition = ''
      this.game_status = 'start'
      this.index_current_proposition = 0
      this.propositions= [ 
        [{}, {}, {}, {}, {}],
        [{}, {}, {}, {}, {}],
        [{}, {}, {}, {}, {}],
        [{}, {}, {}, {}, {}],
        [{}, {}, {}, {}, {}]
      ]

    },
    finish(){
      this.game_status = 'finish'
    }
  },
}
</script>



