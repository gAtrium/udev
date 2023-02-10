<template>
        <p :style="{ fontSize: fontSize + 'em'}">{{ writtentext }}<span ref="blinky" class="blinky">█</span></p>
</template>

<script>
export default {
    props: {
        txt: {
            type: String,
            default: "No default value provided."
        },
        time: {
            type: Number,
            default: -1 //was 0.2
        },
        isLoading: {
            type: Boolean,
            default: false
        },
        fontSize: {
            type: Number,
            default: 3
        }
    },
    data() {
        return {
            writtentext: "",
            towrite: "",
            fontSize: 0,
            interval: null,
            intervaldelay: 0,
            step: 0,
            writing: false
        }
    },
    mounted() {
        this.towrite = this.$props.txt
        this.fontSize = this.$props.fontSize;
        this.writeNew(this.$props.txt);
    },
    methods: {
        writeNew(str = "", until = 0) {
            this.intervaldelay = 0;
            if (this.interval != 0) {
                clearInterval(this.interval)
            }
            if(str != ""){
                this.$refs.blinky.classList.toggle("blinky");
                this.towrite = str;
            }
            if (this.$props.time == -1) {
                this.intervaldelay = 30;
            }
            else this.intervaldelay = (this.$props.time / this.towrite.length) * 1000;
            this.writing = true;
            if(this.writtentext.length - until != 0){
                this.interval = setInterval(this.clearText,this.intervaldelay, until);
            }
            else {
                this.interval = setInterval(this.writeChar,this.intervaldelay, this.writtentext.length);
            }
        },
        writeChar(givenLength = 0) {
            if (this.step >= this.towrite.length + givenLength) {
                this.step = 0;
                console.log("Write char toggle.")
                this.$refs.blinky.classList.toggle("blinky")
                clearInterval(this.interval);
                this.interval = 0;
                this.writing = false
                return;
            }

            if(this.$props.isLoading && this.writing) {
                if(Math.floor(Math.random() * 70) == 29) {
                    //Whoopsie, we made a typo.
                    clearInterval(this.interval)
                    this.writtentext += (Math.random() + 1).toString(36).substring(2,7)[0];
                    setTimeout(()=> {
                        this.writtentext = this.writtentext.slice(0,-1);
                        setTimeout(()=>{
                                this.interval = setInterval(this.writeChar,this.intervaldelay)
                        }, Math.random() * 800);
                    }, Math.random() * 1000)
                    return;
                }
            }
            if(this.writing){
                this.writtentext += this.towrite.charAt(this.step);
                this.step++;
            }
        },
        clearText(until = 0) {
            if(this.writtentext.length - until == 0){
                clearInterval(this.interval);
                this.writing = false
                this.writeNew("", until);
                return;
            }
            this.writtentext = this.writtentext.slice(0,-1);
        },
        isWriting(){
            return this.writing;
        }
        
    }

}
</script>

<style scoped>
p {
    font-family: monospace;
}

.blinky{
    animation: blink 1s steps(1) infinite;
    transition: none;
    left: 0;
    margin-left: 0;
}
.starey {
    animation: none !important;
}

@keyframes blink {
    0% {
        opacity: 1;
    }

    50% {
        opacity: 0;
    }

    100% {
        opacity: 1;
    }
}

#HackerTyperContainer{
    width: 100%;
    vertical-align:middle;
    height: auto;
}
</style>