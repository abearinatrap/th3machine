<template>
    <main>
        <div id="game" oncontextmenu="return false;" class="noselect">
            <button @click="createBoard">{{flags}}</button> 
            <img v-on:click="createBoard" src="../assets/smiley-face.png"/>
            <div id="mine_array">
                <!--
                <div class="mineRow" v-for="objRow in objects" :key="objRow">
                    <div class="mine" v-for="obj in objRow" :key="obj">
                    | {{obj}} |
                    </div>
                </div>
                -->
                <table v-bind:style="tableStyle">
                    <tr v-for="objRow in objects" :key="objRow">
                        <td v-for="obj in objRow" :key="obj" v-bind:style="{ color: obj[2]==-1?'red':'black'}" @click="obj[0]=-1;checkBoard(obj[3],obj[4])" @click.right="flags+=obj[1];obj[1]*=-1">
                            <div v-if="!gameEnd">
                                <div v-if="obj[0]==-1">
                                    {{obj[2]}}
                                </div>
                                <img v-if="obj[1]==-1" src="../assets/flag.png"/>
                            </div>
                            <div v-else>
                                <img v-if="obj[2]==-1&&obj[0]!=-1" src="../assets/bomb.png"/>
                                <img v-if="obj[2]==-1&&obj[0]==-1" src="../assets/wrong-bomb.png"/>
                                <div v-if="obj[2]!=-1">
                                    {{obj[2]}}
                                </div>
                            </div>
                        </td>
                    </tr>
                </table>

               
            </div>
        </div>
    </main>
</template>

<script>

export default ({
    setup() {
        
    },
    data (){
        return{
            flags:0,
            boardWidth:16,
            boardHeight:16,
            numMines:40,
            objects:[[1,2],[1, 3]],
            showObjects:[[1,1],[1,1]],
            gameEnd:true,
            gameWon:false,
            tableStyle:{
                width:"57.5px",
                height:"57.5px"
            }
        }
    },
    methods: {
        createBoard: function(event){
            this.gameEnd=false;
            console.log("yo")
            function addNum(arr,x,y){
                if(arr[x][y][2]<0){
                    return arr[x][y][2];
                }
                return 0;
            }
            console.log("yo")
            // board generation
            this.objects=new Array(this.boardHeight);
            for(let i=0;i<this.boardHeight;i++){
                this.objects[i]= new Array(this.boardWidth)
                for(let j=0;j<this.boardWidth;j++){
                    this.objects[i][j]=[1,1,0,i,j];
                    //console.log(this.objects[i][j])
                }
            }
            let min=1-(this.numMines/(this.boardWidth*this.boardHeight));
            console.log(min)
            let actMines=this.numMines;
            for(let i=0;i<this.boardHeight;i++){
                for(let j=0;j<this.boardWidth;j++){
                    if(actMines==0) break;
                    let ram=Math.random();
                    //console.log(ram)
                    if(ram>min){
                        this.objects[i][j][2]=-1;
                        console.log(i+" "+j+" "+this.objects[i][j])
                        --actMines;
                    }
                }
            }
            console.log(this.objects[1][10])
            
            
            console.log("act"+actMines)
            while(actMines>0){
                let x=Math.floor(Math.random()*this.boardWidth);
                let y=Math.floor(Math.random()*this.boardHeight);   
                //console.log(x+" "+y)
                if(this.objects[y][x][2]==0) {
                    this.objects[y][x][2]=-1;
                    --actMines;
                }
            }

            this.tableStyle.width=28.75*this.boardWidth +'px';
            this.tableStyle.height=28.75*this.boardHeight+'px';
            console.log("board numbers")
            let cc=0;
            for(let i=0;i<this.boardHeight;i++){
                for(let j=0;j<this.boardWidth;j++){
                    if(this.objects[i][j][2]==-1) {cc++}
                }
            }
            console.log("cc"+cc)


            console.log(addNum(this.objects,1,1))
            
            
            //make numbers for board
            for(let i=0;i<this.boardHeight;i++){
                for(let j=0;j<this.boardWidth;j++){
                    if(this.objects[i][j][2]==-1) continue;
                    if(i!=0){
                        if(j!=0){
                            this.objects[i][j][2]-=addNum(this.objects,i-1,j-1)
                        }
                        this.objects[i][j][2]-=addNum(this.objects,i-1,j);
                        if(j<this.boardWidth-1){
                            this.objects[i][j][2]-=addNum(this.objects,i-1,j+1)
                        }
                    }
                    if(j!=0){
                        this.objects[i][j][2]-=addNum(this.objects,i,j-1);
                    }
                    if(j<this.boardWidth-1){
                        this.objects[i][j][2]-=addNum(this.objects,i,j+1);
                    }
                    
                    if(i<this.boardHeight-1){
                        if(j!=0){
                            this.objects[i][j][2]-=addNum(this.objects,i+1,j-1)
                        }
                        this.objects[i][j][2]-=addNum(this.objects,i+1,j)
                        if(j!=this.boardWidth-1){
                            this.objects[i][j][2]-=addNum(this.objects,i+1,j+1)
                        }
                    }
                }
            }
            console.log(this.objects)
            

            //make shown board
            this.showObjects=new Array(this.boardHeight);
            for(let i=0;i<this.boardHeight;++i){
                this.showObjects[i]=new Array(this.boardWidth)
                for(let j=0;j<this.boardWidth;++j){
                    this.showObjects[i][j]=" ";
                }
            }

            
        },
        checkBoard: function(i,j){
            if(this.objects[i][j][2]==-1){
                this.gameEnd=true;
                this.gameWon=false;
                console.log("lost")
            }else if(this.objects[i][j][2]==0){
                //if objects[i][j]==0, bfs for more 0
                this.objects[i][j][0]=1
                this.bfs_z(i,j);
                console.log("bfs")
            }
        },
        bfs_z: function(i,j){
            if(i<0 || i>=this.boardHeight) return;
            if(j<0 || j>=this.boardWidth) return;
            console.log(i+" "+j)
            //this.objects[i-1][j][0]=-1;
            if(this.objects[i][j][0]!=-1){
                this.objects[i][j][0]=-1;
                if(this.objects[i][j][2]!=0) return; 
                this.bfs_z(i-1,j)
                this.bfs_z(i-1,j-1)
                this.bfs_z(i-1,j+1)

                this.bfs_z(i+1,j)
                this.bfs_z(i+1,j-1)
                this.bfs_z(i+1,j+1)

                this.bfs_z(i,j-1)
                this.bfs_z(i,j+1)
            }
        }
    },
    created(){
        console.log("created")
    }
})
</script>

<style scoped>
    #game{
        text-align: center;
        cursor:grab;
    }

    .mineRow{
        width: 100%;
    }

    .mine{
        display: inline-block;
        padding: 5px;
    }

    .revealed {
        background-color: #C0C0C0;
        font-size: 80%;
        text-align: center;
        border: none;
    }

    .noselect {
        -webkit-touch-callout: none; /* iOS Safari */
            -webkit-user-select: none; /* Safari */
            -khtml-user-select: none; /* Konqueror HTML */
            -moz-user-select: none; /* Old versions of Firefox */
                -ms-user-select: none; /* Internet Explorer/Edge */
                    user-select: none; /* Non-prefixed version, currently
                                        supported by Chrome, Edge, Opera and Firefox */
    }

    table {
        display: table;
        border-collapse: separate;
        box-sizing: border-box;
        text-indent: initial;
        border-spacing: 2px;
        border-color: gray;
    }

    td {
        height: 30px;
        width: 30px;
        background-color: #C0C0C0;
        border: 1.5px solid;
        border-top-color: #ffffff;
        border-right-color: #7B7B7B;
        border-bottom-color: #7B7B7B;
        border-left-color: #ffffff;
        text-align: center;
        vertical-align: middle;
        -webkit-filter: brightness(100%);
        filter: brightness(100%);
    }
    td:hover {
        -webkit-filter: brightness(80%);
        filter: brightness(80%);
    }
    

    img {
        filter: brightness(100%);
        -webkit-filter: brightness(100%);
    }
    img:hover {
        filter: brightness(60%);
        -webkit-filter: brightness(60%);
        -webkit-transition: all 1s ease;
        -moz-transition: all 1s ease;
        -o-transition: all 1s ease;
        -ms-transition: all 1s ease;
        transition: all 1s ease;
    }
</style>
