<script lang="ts">
    type Question = { text: string; answer: string };

    type Answer = { text: string; player: number };

    const questions: Question[] = [
        {
            text: "Was ist ein Kafir?",
            answer: "Ein islamisches Wort für einen Ungläubigen.",
        },
        { text: "Fisole ist ein oder ist eine...", answer: "Stadt in Italien" },
    ];

    const totalPlayers = 5;

    let currentQuestion: Question;
    let answers: Answer[];
    let activePlayer: number;
    let showAnswers: boolean;
    let showCorrectAnswer: boolean;

    initRound();

    function initRound() {
        answers = [];
        activePlayer = 0;
        showAnswers = false;
        showCorrectAnswer = false;
        currentQuestion = drawQuestion();
    }

    function drawQuestion() {
        let index = Math.floor(Math.random() * questions.length);
        let question = questions[index];
        questions.splice(index, 1);
        answers.push({ text: question.answer, player: 99 }); // this is the correct answer
        return question;
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

<h1>Unperfekt</h1>

<h3>{currentQuestion?.text}</h3>

{#if !showAnswers}
    <input
        id="answer_input"
        autocomplete="off"
        placeholder={"Du bist dran, Spieler " + (activePlayer + 1)}
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
        <br />
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
