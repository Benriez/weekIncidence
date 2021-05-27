<template>
    <div class="wrapper">
        <div class="box">
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
        </div>
    </div>
</template>


<script>

export default {
  name: 'BgAnimation',
  methods: {

  }
}
</script>


<style lang="sass">
  .wrapper
    position: absolute
    width: 100%
    height: 100%
    overflow: hidden
    z-index: -1
  .box div
    position: absolute
    width: 60px
    height: 60px
    background-color: transparent
    border: 4px solid #efefef

  .box div:nth-child(1)
    top: 12%
    left: 42%
    animation: animate 10s linear infinite
  
  .box div:nth-child(2)
    top: 90%
    left: 50%
    animation: animate 7s linear infinite
  
  .box div:nth-child(3)
    top: 17%
    left: 6%
    animation: animate 9s linear infinite
  
  .box div:nth-child(4)
    top: 20%
    left: 60%
    animation: animate 10s linear infinite
  
  .box div:nth-child(5)
    top: 67%
    left: 10%
    animation: animate 6s linear infinite
  
  .box div:nth-child(6)
    top: 80%
    left: 70%
    animation: animate 12s linear infinite
  
  .box div:nth-child(7)
    top: 60%
    left: 80%
    animation: animate 15s linear infinite
  
  .box div:nth-child(8)
    top: 32%
    left: 25%
    animation: animate 16s linear infinite

  .box div:nth-child(9)
    top: 90%
    left: 25%
    animation: animate 9s linear infinite
  
  .box div:nth-child(10)
    top: 20%
    left: 80%
    animation: animate 5s linear infinite

  @keyframes animate
    0%
      transform: scale(0) translateY(0) rotate(0)
      opacity: 1
    100%
      transform: scale(1.3) translateY(-90px) rotate(360deg)
      opacity: 0
</style>