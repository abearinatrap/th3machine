<template>
    <main>
        <button v-on:click="createBoard">{{boardWidth}}</button>
        <div id="mine_array">
            <div class="mineRow" v-for="objRow in objects" :key="objRow">
                <div class="mine" v-for="obj in objRow" :key="obj">
                   | {{obj}} |
                </div>
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
            boardWidth:16,
            boardHeight:16,
            numMines:40,
            objects:[[1,2],[1, 3]],
            showObjects:{}
        }
    },
    methods: {
        createBoard: function(event){
            
            console.log("yo")
            function addNum(arr,x,y){
                if(arr[x][y]<0){
                    return arr[x][y];
                }
                return 0;
            }
            console.log("yo")
            // board generation
            this.objects=new Array(this.boardHeight);
            for(let i=0;i<this.boardHeight;i++){
                this.objects[i]= new Array(this.boardWidth)
                for(let j=0;j<this.boardWidth;j++){
                    this.objects[i][j]=0;
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
                        this.objects[i][j]=-1;
                        console.log(i+" "+j+" "+this.objects[i][j])
                        --actMines;
                    }else{
                        this.objects[i][j]=0;
                    }
                }
            }
            console.log(this.objects[1][10])
            
            
            console.log("act"+actMines)
            while(actMines>0){
                let x=Math.floor(Math.random()*this.boardWidth);
                let y=Math.floor(Math.random()*this.boardHeight);   
                //console.log(x+" "+y)
                if(this.objects[y][x]==0) {
                    this.objects[y][x]=-1;
                    --actMines;
                }
            }

            console.log("board numbers")
            let cc=0;
            for(let i=0;i<this.boardHeight;i++){
                for(let j=0;j<this.boardWidth;j++){
                    if(this.objects[i][j]==-1) {cc++}
                }
            }
            console.log("cc"+cc)


            console.log(addNum(this.objects,1,1))
            
            
            //make numbers for board
            for(let i=0;i<this.boardHeight;i++){
                for(let j=0;j<this.boardWidth;j++){
                    if(this.objects[i][j]==-1) continue;
                    if(i!=0){
                        if(j!=0){
                            this.objects[i][j]-=addNum(this.objects,i-1,j-1)
                        }
                        this.objects[i][j]-=addNum(this.objects,i-1,j);
                        if(j<this.boardWidth-1){
                            this.objects[i][j]-=addNum(this.objects,i-1,j+1)
                        }
                    }
                    if(j!=0){
                        this.objects[i][j]-=addNum(this.objects,i,j-1);
                    }
                    if(j<this.boardWidth-1){
                        this.objects[i][j]-=addNum(this.objects,i,j+1);
                    }
                    
                    if(i<this.boardHeight-1){
                        if(j!=0){
                            this.objects[i][j]-=addNum(this.objects,i+1,j-1)
                        }

                        if(j!=this.boardWidth-1){
                            this.objects[i][j]-=addNum(this.objects,i+1,j+1)
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

            for(let i=0;i<this.boardHeight;i++){
                for(let j=0;j<this.boardWidth;j++){
                    //console.log(this.objects[i][j])
                }
            }
            
        }
    },
    created(){
        console.log("created")
    }
})
</script>

<style scoped>
    .mineRow{
        width: 100%;
    }

    .mine{
        display: inline-block;
        padding: 5px;
    }
</style>