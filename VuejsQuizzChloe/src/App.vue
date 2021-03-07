<template>
    <div id="app">
        <h1>MON QUIZZ</h1>

        <select v-model="theme">
            <option disabled value="">Choisissez votre th&#232;me</option>
            <option v-for="option in options" v-bind:value="option.value" v-bind:key="option.value">
                {{ option.text }}
            </option>
        </select>

        <input v-model="nombreQuestions" placeholder="Votre nombre de questions">

        <button id="valid" @click="lancer">Validez votre choix</button>

        <div v-if="choixEffectue === true">

            <h1>{{ decodeURIComponent(questions[indexQuestion].question) }}</h1>

            <div v-if="reponseEffectuee === false">
                <button v-for="(reponse, index) in reponses"
                        v-bind:item="reponse"
                        v-bind:key="index"
                        @click="verif(index)">
                    {{ reponse }}
                </button>
            </div>
            <div v-else>

                <h1>{{ resultat }} : la bonne r&#233;ponse est {{ decodeURIComponent(questions[indexQuestion].correct_answer) }}</h1>
                <h1>Votre score {{ final }}{{ nbReponseCorrectes }}/{{ indexQuestion+1 }}</h1>

                <button id="next" @click="next">Question Suivante</button>
            </div>

        </div>
    </div>
</template>

<script>
    import axios from 'axios'

    export default {
        name: 'app',
        data() {
            return {
                questions: [],
                reponses: [],
                indexQuestion: 0,
                correctReponse: 0,
                resultat: 0,
                nbReponseCorrectes: 0,
                choixEffectue: false,
                reponseEffectuee: false,
                nombreQuestion: null,
                final: null,
                theme: '',
                options: [
                    { text: 'Sports', value: 21 },
                    { text: 'G\u00e9ographie', value: 22 },
                    { text: 'Histoire', value: 23 }
                ]
            }
        },
        methods: {
            lancer() {
                axios.get('https://opentdb.com/api.php?amount=' + this.nombreQuestions + '&category=' + this.theme + '&type=multiple&encode=url3986').then(
                    response => {
                        this.questions = response.data.results;
                        this.affecteReponse()
                        this.choixEffectue = true
                    }
                )
            },
            next() {
                this.indexQuestion++
                this.affecteReponse()
            },
            affecteReponse() {
                var i
                this.correctReponse = Math.floor(Math.random() * 4)
                for (i = 0; i < this.correctReponse; i++) this.reponses[i] = decodeURIComponent(this.questions[this.indexQuestion].incorrect_answers[i])
                this.reponses[this.correctReponse] = decodeURIComponent(this.questions[this.indexQuestion].correct_answer)
                for (i = this.correctReponse + 1; i <= 3; i++) this.reponses[i] = decodeURIComponent(this.questions[this.indexQuestion].incorrect_answers[i - 1])
                this.resultat = ""
                this.reponseEffectuee = false

            },
            verif(indexReponse) {
                this.reponseEffectuee = true
                if (this.correctReponse === indexReponse) {
                    this.resultat = "VRAI"
                    this.nbReponseCorrectes++;
                } else {
                    this.resultat = "FAUX"
                }
                if (this.indexQuestion === this.nombreQuestions - 1) {
                    this.final = "FINAL "
                }
            }
        },
        mounted() {

        }
    };
</script>
<style>
</style>

