<script lang="ts">
    type Question = { text: string; answer: string };

    type Answer = { text: string; player: number };

    let totalPlayers = 0;

    let currentQuestion: Question | null;
    let answers: Answer[];
    let activePlayer: number;
    let showAnswers: boolean;
    let showCorrectAnswer: boolean;

    initRound();

    function initRound() {
        currentQuestion = null;
        answers = [];
        activePlayer = 0;
        showAnswers = false;
        showCorrectAnswer = false;
    }

    function shuffleAnswers() {
        let currentIndex = answers.length,
            randomIndex;

        while (currentIndex != 0) {
            randomIndex = Math.floor(Math.random() * currentIndex);
            currentIndex--;
            [answers[currentIndex], answers[randomIndex]] = [
                answers[randomIndex],
                answers[currentIndex],
            ];
        }

        answers = answers;
    }
</script>

{#if totalPlayers == 0}
    <h1>Unperfekt</h1>

    <label for="total_players_input">Wie viele Spieler?</label>
    <input id="total_players_input" type="number" value="4" />
    <button
        on:click={() => {
            totalPlayers = +document.getElementById("total_players_input")
                .value;
        }}>Spielen</button
    >
{:else if !currentQuestion}
    <h3>Spielleiter!</h3>
    <label for="create_question_input">Stelle eine Frage</label>
    <input id="create_question_input" />
    <label for="create_question_answer_input"
        >Gib die richtige Antwort ein</label
    >
    <input id="create_question_answer_input" />
    <button
        on:click={() => {
            let createQuestionInput = document.getElementById(
                "create_question_input"
            );
            let createQuestionAnswerInput = document.getElementById(
                "create_question_answer_input"
            );
            currentQuestion = {
                text: createQuestionInput.value,
                answer: createQuestionAnswerInput.value,
            };
            answers.push({ text: currentQuestion.answer, player: 99 }); // this is the correct answer
            createQuestionInput.value = "";
            createQuestionAnswerInput = "";
        }}>Frage erstellen</button
    >
{:else}
    <h3>{currentQuestion?.text}</h3>

    {#if !showAnswers}
        <label for="answer_input">{"Spieler " + (activePlayer + 1)}</label>
        <input
            id="answer_input"
            autocomplete="off"
            placeholder={"Gib eine falsche aber legitime Antwort!"}
        />
        <button
            on:click={() => {
                let answerInput = document.getElementById("answer_input");

                answers.push({ text: answerInput.value, player: 1 });
                answers = answers;

                answerInput.value = "";
                answerInput.focus();

                if (activePlayer == totalPlayers - 1) {
                    shuffleAnswers();
                    showAnswers = true;
                } else {
                    activePlayer++;
                }
            }}>Fertig</button
        >
    {/if}

    <div>
        {#if showAnswers}
            {#each answers as answer, i}
                <p>
                    {i + 1 + " : " + answer.text}
                    {#if answer.player == 99 && showCorrectAnswer}⬅️{/if}
                </p>
            {/each}
            {#if !showCorrectAnswer}
                <button on:click={() => (showCorrectAnswer = true)}
                    >Zeige richtige Antwort</button
                >
            {:else}
                <button on:click={initRound}>Nächste Frage</button>{/if}
        {/if}
    </div>
{/if}
