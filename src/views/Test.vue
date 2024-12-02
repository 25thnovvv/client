<template>
    <div>
        <h1>Test</h1>

        <div v-if="words.length < 5">
            <p>You need to enter at least five words to begin the test</p>
        </div>
        <div v-else>
            <div>
                <h2>Score: {{ score }} out of {{ words.length }}</h2>

                <form action="#" @submit.prevent="onSubmit">
                    <div class="ui labeled input fluid">
                        <div class="ui label">
                            <i class="germany flag"></i> German
                        </div>
                        <input type="text" readonly :disabled="testOver" :value="currWord.german" />
                    </div>
                    <br>
                    <div class="ui labeled input fluid">
                        <div class="ui label">
                            <i class="united kingdom flag"></i> English
                        </div>
                        <input type="text" placeholder="Enter word..." v-model="english" :disabled="testOver"
                            autocomplete="off" />
                    </div>
                    <br>
                    <button class="ui inverted green button" :disabled="testOver">Submit</button>
                </form>

                <p :class="['results', resultClass]">
                    <span v-html="result"></span>
                </p>
            </div>
        </div>
    </div>
</template>

<script>
import { ViewAllVocabs } from '../helpers/api'; // Import the ViewAllVocabs function from the api helper

export default {
    name: 'test', // Component name
    data() {
        return {
            words: [], // Array to store all vocabulary words
            randWords: [], // Array to store randomized vocabulary words
            incorrectGuesses: [], // Array to store incorrect guesses
            result: '', // String to store the result message
            resultClass: '', // String to store the result class for styling
            english: '', // String to store the user's input
            score: 0, // Integer to store the user's score
            testOver: false // Boolean to indicate if the test is over
        };
    },
    async mounted() {
        // Lifecycle hook that runs when the component is mounted
        this.words = await ViewAllVocabs(); // Fetch all vocabulary words
        this.randWords = [...this.words.sort(() => 0.5 - Math.random())]; // Randomize the vocabulary words
    },
    computed: {
        currWord: function () {
            // Computed property to get the current word
            return this.randWords.length ? this.randWords[0] : ''; // Return the first word in the randomized array or an empty string if the array is empty
        }
    },
    methods: {
        onSubmit: function () {
            // Method to handle form submission
            if (this.english === this.currWord.english) {
                // Check if the user's input matches the current word's English translation
                this.flash('Correct!', 'success', { timeout: 1000 }); // Display a success message
                this.score += 1; // Increment the score
            } else {
                this.flash('Wrong!', 'error', { timeout: 1000 }); // Display an error message
                this.incorrectGuesses.push(this.currWord.german); // Add the incorrect guess to the array
            }
            this.english = ''; // Clear the user's input
            this.randWords.shift(); // Remove the current word from the randomized array
            if (this.randWords.length === 0) {
                // Check if there are no more words left
                this.testOver = true; // Set the testOver flag to true
                this.displayResults(); // Display the results
            }
        },
        displayResults: function () {
            // Method to display the results
            if (this.incorrectGuesses.length === 0) {
                // Check if there are no incorrect guesses
                this.result = 'You got everything correct. Well done!'; // Set the result message for all correct answers
                this.resultClass = 'success'; // Set the result class for styling
            } else {
                const incorrect = this.incorrectGuesses.join(', '); // Join the incorrect guesses into a string
                this.result = `<strong>You got the following words wrong:</strong> ${incorrect}`; // Set the result message for incorrect answers
                this.resultClass = 'error'; // Set the result class for styling
            }
        }
    }
};
</script>

<style scoped>
.results {
    margin: 25px auto;
    padding: 15px;
    border-radius: 5px;
}

.error {
    border: 1px solid #ebccd1;
    color: #a94442;
    background-color: #f2dede;
}

.success {
    border: 1px solid #d6e9c6;
    color: #3c763d;
    background-color: #dff0d8;
}
</style>