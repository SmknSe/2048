<template>
    <div class="wrap" @keyup.left="swipe_left(true)" @keyup.right="swipe_right(true)" @keyup.up="swipe_up(true)"
        @keyup.down="swipe_down(true)" tabindex="0">
        <div class="info">
            <div class="name">2048*</div>
            <div class="score_wrap" style="grid-area: score;">
                <span>
                    score
                </span>
                <span>
                    {{ cur_score }}
                </span>
            </div>
            <div class="score_wrap" style="grid-area: best;">
                <span>
                    best
                </span>
                <span>
                    {{ score }}
                </span>
            </div>
            <div class="buttons" style="grid-area: btn;">
                <button @click="reset">new game</button>
                <button @click="make_backup">back</button>
                <button @click="switch_theme">switch theme</button>
            </div>
        </div>
        <div class="container">
            <div class="game_over" :class="{ visible: isGameOver }">
                <span>Game over</span>
                <span>Your score: {{ score }}</span>
                <button @click="reset">new game</button>
            </div>
            <div class="game_row" v-for="row in field">
                <cell class="game_block" v-for="value in row" :value="value" :classic_style="this.classic_style"></cell>
            </div>
        </div>
    </div>
</template>

<script>
import Cell from './Cell.vue'
export default {
    name: 'Game',
    components: {
        Cell
    },
    data() {
        return {
            field: [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0],],
            backup: [],
            filled_size: 0,
            score: 2,
            cur_score: 2,
            isGameOver: false,
            classic_style: true
        }
    },
    methods: {
        is_game_over() {
            let final_backup = this.copy_array(this.field)
            let backup_filled = this.filled_size
            this.swipe_down(false)
            this.swipe_left(false)
            this.swipe_right(false)
            this.swipe_up(false)
            if (this.compare_arrays(final_backup, this.field)) {
                setTimeout(() => {
                    this.isGameOver = true
                }, 200);
                return
            }
            this.field = this.copy_array(final_backup)
            this.filled_size = backup_filled
        },
        generate_new() {
            let f = true
            let nums = [2, 4]
            while (f) {
                let rand_i = Math.floor(Math.random() * 4)
                let rand_j = Math.floor(Math.random() * 4)
                if (this.field[rand_i][rand_j] == 0) {
                    this.field[rand_i][rand_j] = nums[Math.floor(Math.random())]
                    f = false
                    this.filled_size += 1
                    if (this.filled_size == 16) {
                        this.is_game_over()
                    }
                }
            }
        },
        swipe_up(f) {
            let tmp_backup = this.copy_array(this.field)
            let combined = new Map()
            for (let j = 0; j < this.field.length; j++) {
                for (let i = 0; i < this.field.length; i++) {
                    if (this.field[i][j] != 0) {
                        let f = i - 1
                        while (f > 0 && this.field[f][j] == 0) f--
                        if (f >= 0 && this.field[f][j] == this.field[i][j] && !combined.has('' + f + j)) {
                            this.field[f][j] *= 2
                            if (this.field[f][j] > this.cur_score) {
                                this.cur_score = this.field[f][j]
                            }
                            if (this.field[f][j] > this.score) {
                                this.score = this.field[f][j]
                            }
                            this.filled_size -= 1
                            combined.set('' + f + j, 1)
                            this.field[i][j] = 0
                        }
                        else if (f >= 0 && this.field[f][j] == 0) {
                            this.field[f][j] = this.field[i][j]
                            this.field[i][j] = 0
                        }
                        else if (f >= 0) {
                            if (f !== i - 1) {
                                this.field[f + 1][j] = this.field[i][j]
                                this.field[i][j] = 0
                            }
                        }
                    }
                }
            }
            if (f && !this.compare_arrays(tmp_backup, this.field)) {
                this.backup = tmp_backup
                this.generate_new()
            }
        },
        swipe_left(f) {
            let tmp_backup = this.copy_array(this.field)
            let combined = new Map()
            for (let i = 0; i < this.field.length; i++) {
                for (let j = 0; j < this.field[i].length; j++) {
                    if (this.field[i][j] != 0) {
                        let f = j - 1
                        while (f > 0 && this.field[i][f] == 0) f--
                        if (f >= 0 && this.field[i][f] == this.field[i][j] && !combined.has('' + f + i)) {
                            this.field[i][f] *= 2
                            if (this.field[i][f] > this.cur_score) {
                                this.cur_score = this.field[i][f]
                            }
                            if (this.field[i][f] > this.score) {
                                this.score = this.field[i][f]
                            }
                            this.filled_size -= 1
                            combined.set('' + f + i, 1)
                            this.field[i][j] = 0
                        }
                        else if (f >= 0 && this.field[i][f] == 0) {
                            this.field[i][f] = this.field[i][j]
                            this.field[i][j] = 0
                        }
                        else if (f >= 0) {
                            if (f !== j - 1) {
                                this.field[i][f + 1] = this.field[i][j]
                                this.field[i][j] = 0
                            }
                        }
                    }
                }
            }
            if (f && !this.compare_arrays(tmp_backup, this.field)) {
                this.backup = tmp_backup
                this.generate_new()
            }
        },
        swipe_right(f) {
            let tmp_backup = this.copy_array(this.field)
            let combined = new Map()
            for (let i = 0; i < this.field.length; i++) {
                for (let j = this.field[i].length - 1; j >= 0; j--) {
                    if (this.field[i][j] != 0) {
                        let f = j + 1
                        while (f < this.field[i].length - 1 && this.field[i][f] == 0) f++
                        if (f <= this.field[i].length - 1 && this.field[i][f] == this.field[i][j] && !combined.has('' + f + i)) {
                            this.field[i][f] *= 2
                            if (this.field[i][f] > this.cur_score) {
                                this.cur_score = this.field[i][f]
                            }
                            if (this.field[i][f] > this.score) {
                                this.score = this.field[i][f]
                            }
                            this.filled_size -= 1
                            combined.set('' + f + i, 1)
                            this.field[i][j] = 0
                        }
                        else if (f < this.field[i].length && this.field[i][f] == 0) {
                            this.field[i][f] = this.field[i][j]
                            this.field[i][j] = 0
                        }
                        else if (f < this.field[i].length) {
                            if (f !== j + 1) {
                                this.field[i][f - 1] = this.field[i][j]
                                this.field[i][j] = 0
                            }
                        }
                    }
                }
            }
            if (f && !this.compare_arrays(tmp_backup, this.field)) {
                this.backup = tmp_backup
                this.generate_new()
            }
        },
        swipe_down(f) {
            let tmp_backup = this.copy_array(this.field)
            let combined = new Map()
            for (let j = 0; j < this.field.length; j++) {
                for (let i = this.field.length - 1; i >= 0; i--) {
                    if (this.field[i][j] != 0) {
                        let f = i + 1
                        while (f < this.field.length - 1 && this.field[f][j] == 0) f++
                        if (f <= this.field.length - 1 && this.field[f][j] == this.field[i][j] && !combined.has('' + f + j)) {
                            this.field[f][j] *= 2
                            if (this.field[f][j] > this.cur_score) {
                                this.cur_score = this.field[f][j]
                            }
                            if (this.field[f][j] > this.score) {
                                this.score = this.field[f][j]
                            }
                            this.filled_size -= 1
                            combined.set('' + f + j, 1)
                            this.field[i][j] = 0
                        }
                        else if (f < this.field.length && this.field[f][j] == 0) {
                            this.field[f][j] = this.field[i][j]
                            this.field[i][j] = 0
                        }
                        else if (f < this.field.length) {
                            if (f !== i + 1) {
                                this.field[f - 1][j] = this.field[i][j]
                                this.field[i][j] = 0
                            }
                        }
                    }
                }
            }
            if (f && !this.compare_arrays(tmp_backup, this.field)) {
                this.backup = tmp_backup
                this.generate_new()
            }
        },
        reset() {
            this.isGameOver = false
            this.field = [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0],]
            this.filled_size = 0
            this.cur_score = 2
            this.start()
        },
        copy_array(from) {
            let to = []
            for (let arr of from) {
                to.push(arr.slice(0))
            }
            return to
        },
        compare_arrays(first, second) {
            if (first.length !== second.length) return false
            for (let i = 0; i < 4; i++) {
                for (let j = 0; j < 4; j++) {
                    if (first[i][j] !== second[i][j]) return false
                }
            }
            return true
        },
        make_backup() {
            this.field = this.copy_array(this.backup)
        },
        start() {
            for (let i = 0; i < 2; i++) {
                this.generate_new()
            }
            this.backup = this.copy_array(this.field)
        },
        switch_theme(){
            this.classic_style = !this.classic_style
        }
    },
    mounted() {
        this.start()
    }
}
</script>

<style>
.wrap {
    padding: 50px;
    display: flex;
    flex-direction: column;
    gap: 20px;
    width: 100%;
    min-height: 100vh;
    height: 100%;
    align-items: center;
    overflow: hidden;
}

.wrap:focus {
    outline: none;
}

.info {
    display: grid;
    width: fit-content;
    height: fit-content;
    grid-template-columns: 1fr 100px 100px;
    grid-template-rows: 70px 1fr;
    grid-template-areas:
        'name score best'
        'name btn btn';
    justify-content: center;
    align-items: center;
    justify-items: center;
    gap: 10px;
}

.info>.score_wrap{
    width: 90px;
    height: 100%;
    background-color: rgb(26, 37, 46);
    color: white;
    border-radius: 10px;
}

.name{
    grid-area: name;
    font-size: 4rem;
    color: white;
}

.buttons{
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.container {
    position: relative;
    width: 440px;
    height: 440px;
    padding: 10px;
    display: grid;
    border: solid white 1px;
    grid-template-rows: repeat(4, 1fr);
    border-radius: 20px;
    gap: 10px;
    background-color: rgb(26, 37, 46);
}

.game_over {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    backdrop-filter: brightness(.5) blur(5px);
    border-radius: inherit;
    opacity: 0;
    color: white;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 10px;
    pointer-events: none;
}

.game_over * {
    font-size: 1.5rem;
}

.visible {
    opacity: 1;
    pointer-events: all;
    transition: opacity 1s ease-in-out;
}

.game_row {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
}

.game_block {
    display: flex;
    justify-content: center;
    align-items: center;
    border: solid 1px;
    font-size: 2rem;
    border-radius: 20px;
    transition: all ease-in-out;
}

.score_wrap {
    display: flex;
    flex-direction: column;
    gap: 10px;
    border: solid 1px;
    padding: 5px;
}

.score_wrap span {
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 1rem;
}

button {
    width: 200px;
    padding: 10px;
    background-color: rgb(26, 37, 46);
    color: white;
    border-radius: 10px;
    outline: none;
    border: solid 1px;
    cursor: pointer;
}
</style>