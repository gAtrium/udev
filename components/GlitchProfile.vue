<!--
    -------------------------


        Profile Picture




    Name, surname (Decipher effect)(I forgot)
            position
            skills
    --------------------------
            
-->

<template>
    <div class="GlitchProfile" style="--c:repeating-linear-gradient(45deg,white,white);--w:40px;--b:2px;--r:20px;"> <!-- I stole this from  https://stackoverflow.com/a/61913549-->
        <div class="GlitchImageContainer">
            <img :src="this.imgsrc" alt="{{ $props.devName }}" srcset="" />
            <img ref="imgtag" :src="this.imgsrc" alt="{{ $props.devName }}" srcset="">
        </div>
        <div class="GlitchInfoBox">
            <span>{{ this.$props.devName }}</span><br /><br />
            <span>{{ this.$props.position }}</span>
        </div>
        <hr style="width:50%"/>
        <div class="GlitchSkillBox">
            <ul id="example-1">
                <li v-for="skill in this.$props.skills">
                    {{ skill }}
                </li>
            </ul>
        </div>
    </div>
</template>

<style>
.GlitchProfile {
    --b: 5px;
    /* thickness of the border */
    --c: rgb(255, 255, 255);
    /* color of the border */
    --w: 20px;
    /* width of border */
    --r: 25px;
    /* radius */


    padding: var(--b);
    /* space for the border */

    position: relative;

    width: fit-content;
    height: fit-content;
}


/* https://youtu.be/Rsm5B1GVp5Q?t=13 */
.GlitchProfile::before {
    content: "";
    position: absolute;
    inset: 0;
    background: var(--c, red);
    padding: var(--b);
    border-radius: var(--r);
    -webkit-mask:
        linear-gradient(0deg, #000 calc(2*var(--b)), #0000 0) 50% var(--b)/calc(100% - 2*var(--w)) 100% repeat-y,
        linear-gradient(-90deg, #000 calc(2*var(--b)), #0000 0) var(--b) 50%/100% calc(100% - 2*var(--w)) repeat-x,
        linear-gradient(#000 0 0) content-box,
        linear-gradient(#000 0 0);
    -webkit-mask-composite: destination-out;
    mask-composite: exclude;

}

.GlitchImageContainer {
    width: 200px;
    height: 200px;
    position: relative;

}

.GlitchImageContainer img {
    width: 200px;
    height: 200px;
    position: absolute;
    border-top-left-radius: 20px;
    border-top-right-radius: 20px;
    top: 0;
    left: 0;
    object-fit: cover;
}

.GlitchInfoBox {
    font-family: monospace;
    border-top-width: 2px;
    border-top-color:white;
    border-top-style: solid;
    text-align: center
}
</style>
<script>

export default {
    props: {
        _imgsrc: {
            type: String,
            default: "Cockatiel.jpg"
        },
        devName: {
            type: String,
            default: "Small berd"
        },
        position: {
            type: String,
            default: "Screaming"
        },
        skills: {
            type: Array,
            default: ["Pecking", "Eating", "Screaming"]
        }
    },
    data() {
        return {
            imgsrc: "",
            glitchstage: 0,
        }
    },
    beforeMount() {
        this.imgsrc = new URL(`../assets/${this.$props._imgsrc}`, import.meta.url);
    },
    mounted() {
        setTimeout(this.glitchIt, 300);
    },
    methods: {
        glitchIt() {
            switch (stage) {
                case 0: //glitch it
                    const clipTop = Math.floor(Math.random() * 80);
                    const clipBottom = clipTop + Math.floor(Math.random() * 80);
                    const translateX = Math.floor(Math.random() * 40) - 20;
                    const translateY = 0
                    this.$refs.imgtag.style.clip = `rect(${clipTop}px, 9999px, ${clipBottom}px, 0)`;
                    this.$refs.imgtag.style.transform = `translate(${translateX}px, ${translateY}px)`;
                    stage = 1
                    //TODO: Have another image in the background and clip it so it looks like it's being torn
                    setTimeout(this.glitchIt, Math.random() * 100);
                    break;
                default:
                case 1: //revert
                    stage = 0;
                    this.$refs.imgtag.style.clip = ``;
                    this.$refs.imgtag.style.transform = ``;
                    setTimeout(this.glitchIt, Math.random() * 1000);
                    break;
            }
        }
    }
}
var stage = 0;
</script>