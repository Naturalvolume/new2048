<template>
    <div class="singel-wrapper">
        <div class="all-wrapper">
            
            <!-- 11.28 开始游戏，已具备重新游戏的功能 -->
            <div class="btn-wrapper">

                <div class="title-func-wrapper">
                    <div class="title-wrapper">
                        <div class="title">
                            <span>2</span><span>0</span><span>4</span><span>8</span>
                        </div>
                    </div>

                    <div class="func-wrapper">
                        <div class="func-btn-wrap">
                            <div class="mode-wrap">
                                <!-- <div>模式选择</div> -->
                                <div v-if="![0,1,2].includes(type)">模式选择</div>
                                <div v-else>{{modeItem[type].mode}}</div>
                                <ul>
                                    <li v-for="(item, index) in modeItem" :key="item.mode">
                                        <button :class="{'active': item.isModeActive}" @click="modeClick(index)">{{item.mode}}</button>
                                    </li>
                                </ul>
                            </div>

                            <button class="start" @click="startGame">开始游戏</button>
                            <button class="back" @click="() => this.$router.replace('/')">返回菜单</button>
                        </div>
                    </div>
                </div>

                <div class="score-wrap">
                    <div class="classic-wrap score">
                        <p>分数</p>
                        <p v-if="inited">{{this.$refs.gameboard.myScore}}</p>
                    </div>
                    <div class="classic-wrap best-score">
                        <p>历史最高</p>
                        <p>{{bestScore}}</p>
                    </div>

                    <div v-if="modeItem[1].isModeActive || modeItem[2].isModeActive" class="classic-wrap timer-wrap">
                        <div v-if="modeItem[1].isModeActive" class="limit-clock">
                            <p>限时时间</p>
                            <p>{{this.$refs.gameboard.time}}</p>
                        </div>
                        <div v-if="modeItem[2].isModeActive" class="clock">
                            <p>计时时间</p>
                            <p>{{this.$refs.gameboard.count}}</p>
                        </div>
                    </div>
                </div>
            </div>
            <!-- 11.28 删除一些监听事件 -->
            <div class="game-wrapper">
                <GameBoard :type="type" class="game" @gameOver="gameOver" ref="gameboard" />
            </div>
            <!-- 11.28 未做：在gameover和succes函数中控制游戏结果显示变量 
            -->
            <div v-if="scoreShow" class="result-mask">
                <div class="result-wrap">
                    <div class="my-result-wrapper">
                        <p class="result-title">我的分数</p> 
                        <p class="result-content">{{this.$refs.gameboard.myScore}}</p>

                        <template v-if="type===2">
                            <p class="result-title">用时</p>
                            <!-- 计时模式：获取游戏用时 -->
                            <p class="result-content">{{this.$refs.gameboard.time}}</p> 
                        </template>
                        <div>
                            <button class="back" @click="() => scoreShow = false">返回</button>
                        </div>
                    </div>
                </div>
            </div>    
        </div>
    </div>
</template>

<script>
import GameBoard from '../components/Gameboard'
export default {
    components: {
        GameBoard,
    },
    data () {
        return {
            modeItem: [
                {
                  // 11.28 默认经典模式
                    mode: '经典模式',
                    isModeActive: true
                }, 
                {
                    mode: '限时模式',
                    isModeActive: false
                }, 
                {
                  // 11.28 
                    mode: '速度模式',
                    isModeActive: false
                }
            ],
            // 11.28 用不到的变量
            // score: 0,
            bestScore: 0,
            // limitTime: 0,
            // clockTime: 0,
            isGameover: true,
            // 11.28 不能在一开始就展示分数，否则 this.$ref 取不到
            scoreShow: false,
            // 11.28 新增变量
            type: 0,
            inited: false
        }
    },
    mounted() {
      // 11.28 这里 params是空对象
        // this.score = this.$route.params.myScore
        console.log(this.$refs)
        this.inited = true
        // this.score = this.$refs.gameboard.myScore
        // this.limitTime = this.$refs.gameboard.time
        // this.clockTime = this.$refs.gameboard.count
    },
    methods: {
        modeClick (index) {
            this.modeItem.forEach((item, indx) => {
                if (index === indx) {
                    this.type = index
                    console.log(this.type)
                    item.isModeActive = true
                } else {
                    item.isModeActive = false
                }
            })
        },
        startGame () {
            this.$refs.gameboard.init()
        }, 
        gameOver () {
          // 11.28 加上游戏结束动画／弹窗
            this.scoreShow = true
        }
    }
}
</script>

<style lang="scss" scoped>
%default-button {
    // width: 100%;
    // height: 45px;
    // line-height: 45px;
    font-size: 24px;
    background-color: #8C7B69;
    color: #F9F6F3;
    border-radius:1rem;
    border-width: 0;
    // margin: 20px 0;
}
%position-center {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}
$button-background: #8C7B69;
$content-width: 55vw;
%content-wrap {
    background-color: rgba(0,0,0,0.5);
    border-radius: 1rem;
}

.result-mask {
    // z-index: 999;
    position: absolute;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background-color: rgba(0, 0, 0, 0.6);

    .result-wrap {
        @extend %position-center;
        width: $content-width;
        text-align: center;

        .my-result-wrapper {
            padding: 3%;
            font-size: 2rem;
            color: #EBE0CB;
            font-weight: bold;
            font-style: italic;
            background-color: rgba(255,255,255,0.2);
            box-shadow: 2px 3px 28px 16px;
            border-radius: 1rem;

            .result-title {
                border-bottom: 0.1rem solid #efdbaa;
                padding: 3%;
                margin-bottom: 3%;
                @extend %content-wrap;
            }
            .result-content {
                @extend %content-wrap;
            }
            .result-content {
                margin-bottom: 3%; 
            }

            .back {
                background: transparent;
                color: #b4a799;
                margin: 10px;
                font-size: 16px;
            }
            .back::before {
                content: '';
                display: inline-block;
                width: 0;
                height: 0;
                border: 5px solid #b4a799;
                border-color: transparent transparent transparent #766E66;
            }
        }
    }
}

@media only screen and (max-width: 466px){
    @mixin default-button () {
        // width: 100%;
        // height: 45px;
        // line-height: 45px;
        font-size: 24px;
        background-color: #8C7B69;
        color: #F9F6F3;
        border-radius:1rem;
        border-width: 0;
        // margin: 20px 0;
    }
    @mixin position-center () {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
    }
    .singel-wrapper {
        width: 100%;
        height: 100%;

        .all-wrapper {
            width: 100%;
            height: 100%;
            margin: 5vw;
            position: relative;
            top: 0;
            margin: 0;
            
            .btn-wrapper {
                position: relative;
                height: 40%;

                .title-func-wrapper {
                    position: absolute;
                    width: 100%;
                    height: 70%;
                    top: 0;

                    .title-wrapper {
                        position: absolute;
                        height: 100%;
                        width: 50%;
                        left: 0;
                        .title {
                            position: absolute;
                            width: 10rem;
                            height: 10rem;
                            line-height: 10rem;
                            text-align: center;
                            font-weight: 700;
                            font-size: 4rem;
                            border: 3px solid #E9CF7F;
                            border-radius: 1rem;
                            right: 5vh;
                            bottom: 5vh;
                            box-sizing: border-box;

                            span:nth-child(1) {
                                color: #F59563;
                            }
                            span:nth-child(2) {
                                color: #F65E3B;
                            }
                            span:nth-child(3) {
                                color: #EDCD61;
                            }
                            span:nth-child(4) {
                                color: rgb(255, 187, 85);
                            }
                        }
                    }

                    .func-wrapper {
                        position: absolute;
                        height: 100%;
                        width: 50%;
                        right: 0;
                        .func-btn-wrap {
                            position: absolute;
                            left: 5vh;
                            bottom: 5vh;
                            width: 100px;
                            height: 100px;
                            // display: flex;
                            // align-items: space-between;
                            text-align: center;

                            .mode-wrap:hover{
                                height: 100px;
                                position: absolute;
                            }
                            .mode-wrap > div::before {
                                content: '';
                                display: inline-block;
                                width: 0;
                                height: 0;
                                border: 5px solid #766E66;
                                border-color: transparent transparent transparent #F67D60;
                            }
                            .mode-wrap {
                                // position: absolute;
                                // top: 0;
                                // right: calc(15vw / 2);
                                overflow: hidden;
                                height: 26px;
                                cursor: pointer; 
                                transition: all 0.35s;
                                font-size: 14px;
                                background-color: #8C7B69;
                                color: #F9F6F3;
                                padding: 0px 5px;
                                border-radius: 8px;
                                border: 3px solid #E9CF7F;
                                z-index: 999;
                                box-sizing: border-box;
                                width: 100px;

                                div {
                                    box-sizing: border-box;
                                    height: 20px;
                                    padding: 0px 5px;
                                }
                                ul > li {
                                    box-sizing: border-box;
                                    height: 20px;
                                    margin: 5px 0;
                                    button {
                                        font-size: 14px;
                                        background: #766553;
                                        color: #EBE0CB;
                                        padding: 0px 5px;
                                    }
                                    button:hover {
                                        cursor: pointer;
                                        background-color: #EBE0CB;
                                        color: #766E66;
                                        font-weight: bolder;
                                    }
                                }
                                // 11.28 更改按钮样式
                                .active {
                                    font-weight: 700;
                                    color: #F67D60;
                                }
                            }
                            // .mode-wrap,
                            .start,
                            .back {
                                @include default-button();
                                width: 100%;
                                font-size: 16px;
                                display: block;
                                margin-top: 15px;
                            }
                        }
                    }
                
                }

                .score-wrap {
                    width: 100%;
                    height: 30%;
                    position: absolute;
                    bottom: 0;
                    display: flex;
                    justify-content: space-around;
                    

                    .classic-wrap {
                        // position: absolute;
                        border: 3px solid #E9CF7F;
                        border-radius: 8px;
                        // top: 40px;
                        text-align: center;
                        font-size: 2rem;
                        padding: 0.5rem;
                        min-width: 8rem;

                        p:nth-child(1) {
                            border-bottom: 1px solid #E9CF7F;
                            padding-bottom: 0.5rem;
                        }
                    }
                    .score {
                        color: #EDCD61;
                    }
                    .best-score {
                        color: #ffbb55
                    }
                    .timer-wrap {
                        color: #F65E3B;
                    }
                }
            }
            .game-wrapper {
                position: relative;
                height: 60%;
                width: 100%;
                // max-height: 439.19px;
                // max-width: 466px;
                .game {
                    // width: 95%;
                    position: absolute;
                    bottom: 4vh;
                    left: calc(15vw / 2);
                }
            }
            // .result-wrap {
            //     @include position-center();
            //     width: 200px;
            //     height: 200px;
            //     border: 1px solid black;
            //     background-color: rgba(0,0,0,0.5);
            //     z-index:10000;
            //     color: #fff;
            // }
        }
    }
}

@media only screen and (min-width: 466px){
    @mixin default-button () {
        // width: 100%;
        // height: 45px;
        // line-height: 45px;
        font-size: 24px;
        background-color: #8C7B69;
        color: #F9F6F3;
        border-radius:10px;
        border-width: 0;
        // margin: 20px 0;
    }
    @mixin position-center () {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
    }
    .singel-wrapper {
        width: 100%;
        height: 100%;
        position: relative;

        .all-wrapper {
            width: 466px;
            height: 789px;
            margin: auto;
            position: position;
            
            .btn-wrapper {
                position: relative;
                height: 40%;

                .title-func-wrapper {
                    position: absolute;
                    width: 100%;
                    height: 70%;
                    top: 0;

                    .title-wrapper {
                        position: absolute;
                        height: 100%;
                        width: 50%;
                        left: 0;
                        .title {
                            position: absolute;
                            width: 100px;
                            height: 100px;
                            line-height: 100px;
                            font-weight: 700;
                            font-size: 40px;
                            border: 3px solid #E9CF7F;
                            border-radius: 1rem;
                            right: 5vh;
                            bottom: 5vh;
                            box-sizing: border-box;

                            span:nth-child(1) {
                                color: #F59563;
                            }
                            span:nth-child(2) {
                                color: #F65E3B;
                            }
                            span:nth-child(3) {
                                color: #EDCD61;
                            }
                            span:nth-child(4) {
                                color: rgb(255, 187, 85);
                            }
                        }
                    }

                    .func-wrapper {
                        position: absolute;
                        height: 100%;
                        width: 50%;
                        right: 0;
                        .func-btn-wrap {
                            position: absolute;
                            left: 5vh;
                            bottom: 5vh;
                            width: 100px;
                            height: 100px;
                            // display: flex;
                            // align-items: space-between;
                            text-align: center;

                            .mode-wrap:hover{
                                height: 100px;
                                position: absolute;
                            }
                            .mode-wrap > div::before {
                                content: '';
                                display: inline-block;
                                width: 0;
                                height: 0;
                                border: 5px solid #766E66;
                                border-color: transparent transparent transparent #F67D60;
                            }
                            .mode-wrap {
                                // position: absolute;
                                // top: 0;
                                // right: calc(15vw / 2);
                                overflow: hidden;
                                height: 26px;
                                cursor: pointer; 
                                transition: all 0.35s;
                                font-size: 14px;
                                background-color: #8C7B69;
                                color: #F9F6F3;
                                padding: 0px 5px;
                                border-radius: 8px;
                                border: 3px solid #E9CF7F;
                                z-index: 999;
                                box-sizing: border-box;
                                width: 100px;

                                div {
                                    box-sizing: border-box;
                                    height: 20px;
                                    padding: 0px 5px;
                                }
                                ul > li {
                                    box-sizing: border-box;
                                    height: 20px;
                                    margin: 5px 0;
                                    button {
                                        font-size: 14px;
                                        background: #766553;
                                        color: #EBE0CB;
                                        padding: 0px 5px;
                                    }
                                    button:hover {
                                        cursor: pointer;
                                        background-color: #EBE0CB;
                                        color: #766E66;
                                        font-weight: bolder;
                                    }
                                }
                                // 11.28 更改按钮样式
                                .active {
                                    font-weight: 700;
                                    color: #F67D60;
                                }
                            }
                            // .mode-wrap,
                            .start,
                            .back {
                                @include default-button();
                                width: 100%;
                                font-size: 16px;
                                display: block;
                                margin-top: 15px;
                            }
                        }
                    }
                
                }

                .score-wrap {
                    width: 100%;
                    height: 30%;
                    position: absolute;
                    bottom: 0;
                    display: flex;
                    justify-content: space-around;
                    

                    .classic-wrap {
                        // position: absolute;
                        border: 3px solid #E9CF7F;
                        border-radius: 8px;
                        // top: 40px;
                        min-width: 4em;
                        text-align: center;
                        font-size: 24px;
                        padding: 5px;

                        p:nth-child(1) {
                            border-bottom: 1px solid #E9CF7F;
                            padding-bottom: 5px;
                        }
                    }
                    .score {
                        color: #EDCD61;
                    }
                    .best-score {
                        color: #ffbb55
                    }
                    .timer-wrap {
                        color: #F65E3B;
                    }
                }
            }
            .game-wrapper {
                position: relative;
                height: 60%;
                width: 100%;
                display: flex;
                justify-content: center;
                align-items: center;
            }
            // .result-wrap {
            //     @include position-center();
            //     width: 200px;
            //     height: 200px;
            //     border: 1px solid black;
            //     background-color: rgba(0,0,0,0.5);
            //     z-index:10000;
            //     color: #fff;
            // }
        }
    }
}
</style>