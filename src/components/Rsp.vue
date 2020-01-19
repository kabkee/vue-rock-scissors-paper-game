<template>
    <v-container class="grey lighten-5">
        <v-row justify="center">
            <v-col cols="8">
                <v-row>
                    <v-col cols="12" align="center">
                        <h1 class='display-3 gameResult' :class='[lowerGameResult]'>{{gameResult}}</h1>
                        <h1 class='title'>WIN: <span class='gameResult win'>{{winCount}}</span> LOST: <span class='gameResult lost'>{{lostCount}}</span> Draw: <span class='gameResult draw'>{{drawCount}}</span></h1>
                        <h1 class='.subtitle-1'>WIN Rate: <span class='title'>{{winRate}}</span> %</h1>
                    </v-col>
                </v-row>
            </v-col>
            <v-col cols="8">
                <v-row>
                    <v-col cols="6" align="center">
                        <h1>Computer</h1>
                        <div class='resultWrap' align-self='center'>
                            <div ref='compRsp' justify='center' class="rspResult" :class="[compResultTxt, rollingSetTimeout?'rolling':'']" :style="{ backgroundImage: `url(${rspImg})` }"></div>
                        </div>
                    </v-col>
                    <v-col cols="6" align="center">
                        <h1>USER</h1>
                        <div class='resultWrap' align-self='center'>
                            <div justify='center' class="rspResult" :class="[userResultTxt]" :style="{ backgroundImage: `url(${userResultTxt?rspImg:''})` }"></div>
                        </div>
                    </v-col>
                </v-row>
                <v-row justify="center">
                    <v-col cols="8" align="center">
                        <div align-self='center'>
                            <v-row>
                                <v-col cols="4" align="center">
                                    <div justify='center' class="pa-2 selectable rspResult rock" :class='{active: userResult==1}' :style="{ backgroundImage: `url(${rspImg})` }" @click='onSelectUserRsp(1)'></div>
                                </v-col>
                                <v-col cols="4" align="center">
                                    <div justify='center' class="pa-2 selectable rspResult" :class='{active: userResult==2}' :style="{ backgroundImage: `url(${rspImg})` }" @click='onSelectUserRsp(2)'></div>
                                </v-col>
                                <v-col cols="4" align="center">
                                    <div justify='center' class="pa-2 selectable rspResult paper" :class='{active: userResult==3}' :style="{ backgroundImage: `url(${rspImg})` }" @click='onSelectUserRsp(3)'></div>
                                </v-col>
                            </v-row>
                        </div>
                    </v-col>
                </v-row>
            </v-col>
        </v-row>
    </v-container>
</template>

<script>
import rspLogo from '@/assets/img/rsp.png'
export default {
    name: "Rsp",
    props: {
        msg: String
    },
    data() {
        return {
            rspImg: rspLogo,
            prevCompResult: null,
            compResult: null,
            userResult: null,
            rollingSetTimeout: null,
            gameResult: '',
            winCount: 0,
            lostCount: 0,
            drawCount: 0,
        };
    },
    mounted() {
        this._rollingCompRsp();
    },
    computed: {
        compResultTxt(){
            return this._convertResultToString(this.compResult);
        },
        userResultTxt(){
            if(this.userResult){
                return this._convertResultToString(this.userResult);
            }
            return '';
        },
        lowerGameResult(){
            if(this.gameResult){
                return this.gameResult.toLowerCase();
            }
            return '';
        },
        winRate(){
            if(this.winCount){
                return parseFloat( this.winCount / (this.winCount + this.lostCount + this.drawCount) * 100).toFixed(1);
            }
            return 0;
        },
    },
    methods: {
        _convertResultToString(resultNumb){
            switch(resultNumb){
                case 1:
                    return 'rock';
                case 2:
                    return 'scissors';
                case 3:
                    return 'paper';
                default:
                    return 'rock';
            }
        },
        _getRandResult(){
            this.prevCompResult = this.compResult;
            let newRand = Math.floor(Math.random() * 3)+1;
            while(this.prevCompResult == newRand){
                this._getRandResult();
                return;
            }
            this.compResult = newRand;
        },
        playGame: function() {
            this._rollingCompRsp();
            this.userResult = null;
            this.gameResult = null;
        },
        _getResult: function(){
            this._stopRollingCompRsp();
            if(this.compResult == this.userResult){
                // 비김
                this.gameResult = 'DRAW';
                this.drawCount += 1;
            }else if(
                this.compResult == 1 && this.userResult == 2 ||
                this.compResult == 2 && this.userResult == 3 ||
                this.compResult == 3 && this.userResult == 1
            ){
                // comp win
                this.gameResult = 'LOST';
                this.lostCount += 1;
            }else{
                // user win
                this.gameResult = 'WIN';
                this.winCount += 1;
            }

            setTimeout(() => {
                this.playGame();
            }, 3000);
        },
        _rollingCompRsp(){
            if(this.rollingSetTimeout){
                clearInterval(this.rollingSetTimeout);
            }
            this.rollingSetTimeout = setInterval(() => {
                this._getRandResult()
            }, 500);
        },
        _stopRollingCompRsp(){
            if(this.rollingSetTimeout){
                clearInterval(this.rollingSetTimeout);
                this.rollingSetTimeout = null;
            }
        },
        onSelectUserRsp(rspResultNumb){
            if(this.gameResult){
                return;
            }
            this.userResult = rspResultNumb;
            this._getRandResult();
            this._getResult();
        },
    },
};
</script>

<style scoped lang='scss'>
.resultWrap{
    min-height: 150px;
    display: flex;
}
.rspResult {
    height: 85px;
    width: 100%;
    align-self: center;
    background-position-y: -100px;
    background-position-x: center;
    background-repeat-y: repeat;
    transition: 0.4ms;
    opacity: 1;
 
    &.rock {
        background-position-y: -8px;
    }
    &.paper{
        background-position-y: 85px;
    }

    &.rolling{
        opacity: 0.5;
    }
    &.selectable{
        display: inline-block;
        width: inherit;
        box-shadow: 0 16px 16px rgb(0, 0, 0, 0.5);
        cursor: pointer;
        opacity: 0.5;

        &:hover{
            box-shadow: 0 5px 5px rgb(0, 0, 0, 0.5);
            border: 4px solid #999;
            opacity: 1;
        }
         &.active{
            border: 4px solid rgb(53, 53, 255);
            opacity: 1;
         }
    }
}
.gameResult{
    min-height: 60px;

    &.draw{
        color: #333;
    }
    &.win{
        color: green;
    }
    &.lost{
        color: red;
    }
}

</style>
