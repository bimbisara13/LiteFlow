<template>
  <div class="container">
    <h1 class="fancy">LiteFlow</h1>
    <RouterView />
  </div>
</template>

<script>
export default {
  name: 'App'
};
</script>

<style>
body {
  font-family: 'Poppins', sans-serif;
  background-color: #e4efe7;
}

h1 {
  margin: 25px 5px;
  text-align: center;
}

.container {
  margin: 25px auto;
  padding: 0px 25px;
}

.fancy {
  font-size: 2.25em;
  position: absolute;
  left: 50%;
  transform: translate(-50%, -50%);
  background-image: linear-gradient(#eed202, #eed202);
  background-repeat: no-repeat;
  background-position: 0% 100%;
  background-size: 100% 8px;
  transition: background-size .5s, background-position .5s ease-in-out;
  -webkit-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.fancy:hover {
  background-position: 50% 0%;
  background-size: 100% 100%;
  transition: background-position .5s, background-size .5s ease-in-out;
}
</style>
