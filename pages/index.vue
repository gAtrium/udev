<template>
    <div id="indexContent">
        <HackerTyperText ref="influence" id="Influence" txt="Your community" fontSize="5" isLoading="true" />
            <p class="bio">
                Lorem ipsum dolor sit amet consectetur adipisicing elit.
            </p>
    </div>
</template>

<style>
#indexContent {
    width: 100%;
    height: 100%;
}
#indexContent::before {
    content: '';
    position: absolute;
    left: 0;
    top: 0;
    inset: 0;
    opacity: 0.1;
    background-image: url('~/assets/logo-test.png');
    background-repeat: no-repeat;
    background-position: 50% 0;
    background-size: contain;
}

#Influence {
    margin-top: 2em;
    text-align: center;
}
.bio {
    height: 100%;
    margin-top: 4em;
    font-family: monospace;
    font-size: 3rem;
    text-align: center;
}
</style>
<script>
export default{
    mounted() {
        setInterval(()=> {
      var lol = ["community", "opportunities", "friends", "career", "I have no idea"]
      //print(lol[Math.random() * lol.length])
      if(this.$refs.influence.isWriting() == 0) this.$refs.influence.writeNew(lol[Math.floor(Math.random() * lol.length)], "Your ".length) 
    }, Math.random() * 12000);
    }
}
</script>