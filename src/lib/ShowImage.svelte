<script>
  export let show;
</script>

<img class="revived-show-img" src={"img/" + show.pic} alt={show.name + " title card"} >

<style>

  @media screen and (max-width: 480px) {
    .revived-show-img {
      width: 45vw;
      margin: 1em;
    }
  }

  @media screen and (min-width: 480px) {
    .revived-show-img {
      width: 20vw;
    }
  }

  .revived-show-img {
    margin: auto;
  }
</style>