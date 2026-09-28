<script setup>
import { ref, computed } from 'vue';

const MAX_BOXES = 9;

const player1 = ref('X');
const player2 = ref('O');

const player1Played = ref(false);
const gameOver = ref(false);
const finalResult = ref('');

const initBoard = () => {
   return Array.from({ length: MAX_BOXES }, (_, index) => "")
};

const board = ref(
    initBoard()
);

const isDraw = computed(() => {
    return board.value.every((box) => ['O', 'X'].includes(box) );
});

const winningPatterns = [
    // Rows
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],

    // Columns
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],

    // Diagonals
    [0, 4, 8],
    [2, 4, 6]
];

const winner = (player) => {
    return patternMatched() ? player : null;
};

const patternMatched = () => {    
    for(let pattern of winningPatterns){
        const value1 = board.value[pattern[0]] ?? '';
        const value2 = board.value[pattern[1]] ?? '';
        const value3 = board.value[pattern[2]] ?? '';

        if(!value1 || !value2 || !value3)
            continue;

        if( (value1 === value2 && value2 === value3) ){
            gameOver.value = true;
            break;
        }
    }

    return gameOver.value;
};

const currentPlayer = computed(() => {
    return player1Played.value ? player2 : player1;
});

function handleClick(currentIndex) {
    const player = currentPlayer.value;

    board.value[currentIndex] = player.value;

    const res = winner(player.value);
    if(gameOver.value){
        switch(res){
            case "X":
                finalResult.value = 'X Won!';
                break;
            case "O":
                finalResult.value = 'O Won!';
                break;
            default:
                finalResult.value = 'Draw';
        }
    }

    if(isDraw.value){
        gameOver.value = true;
        finalResult.value = "Draw";
        return;
    }

    player1Played.value = !player1Played.value;
};

const restart = () => {
    board.value = initBoard();
    gameOver.value = false;
    finalResult.value = '';
    player1Played.value = false;
};
</script>

<template>
    <div
        class="w-screen h-screen flex flex-col items-center justify-center space-y-4"
    >
        <h3 v-if="!gameOver" class="text-lg font-bold">
            <span :class="{ 'text-[var(--x)]': currentPlayer === 'X', 'text-[var(--o)]': currentPlayer === 'O' }">{{ currentPlayer }}</span><span>'s Turn</span>
        </h3>
        <div class="grid grid-cols-3 grid-rows-3">
            <button
                v-for="(box, index) in board"
                :disabled="gameOver || box !== ''"
                :key="index"
                @click="handleClick(index)"
                class="w-[100px] h-[100px] font-semibold border border-[var(--border)] hover:border-[var(--border-hover)] bg-[var(--cell)] cursor-pointer text-xl"
                :class="{ 'text-[var(--x)] shadow-[0_0_8px_var(--x-glow)]': box === 'X',
                        'text-[var(--o)] shadow-[0_0_8px_var(--o-glow)]': box === 'O' }"
            >
                {{ box }}
            </button>
        </div>

        <div v-if="gameOver" :class="{ 'text-[var(--warning)]': isDraw, 'text-[var(--success)]': !isDraw }">{{ finalResult }}</div>

        <div>
            <button class="btn-primary" @click="restart()" type="button">Restart</button>
        </div>
    </div>
</template>

<style scoped>

</style>