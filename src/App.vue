<script setup>
import { ref, computed } from 'vue';

const MAX_BOXES = 9;

const player1 = ref('X');
const player2 = ref('O');

const player1Played = ref(false);
const gameOver = ref(false);
const finalResult = ref('');

const board = ref(
    Array.from({ length: MAX_BOXES }, (_, index) => "")
);

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
    if(patternMatched())
        return player;
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
                finalResult.value = 'Player 1 Won!';
                break;
            case "O":
                finalResult.value = 'Player 2 Won!';
                break;
            default:
                finalResult.value = 'Draw';
        }
    }

    player1Played.value = !player1Played.value;
}
</script>

<template>
    <div
        class="w-screen h-screen flex flex-col items-center justify-center bg-[var(--bg)] text-[var(--text)]"
    >
        <div class="grid grid-cols-3 grid-rows-3">
            <button
                v-for="(box, index) in board"
                :disabled="gameOver || box !== ''"
                :key="index"
                @click="handleClick(index)"
                class="w-[50px] h-[50px] font-semibold border border-[var(--border)] bg-[var(--cell)] text-[var(--text)] cursor-pointer"
            >
                {{ box }}
            </button>
        </div>

        <div v-if="gameOver">{{ finalResult }}</div>
    </div>
</template>

<style scoped>

</style>