<template>
  <div id="app">
    <img width="300px" src="./assets/logo_augmentedciso.png" />
    <p>
      Votre objectif: développer un composant permettant de représenter une
      matrice comme celle ci-dessous.
    </p>
    <img width="725px" src="./assets/gitlab.png" />

    <hr />

      <commitGrid />

    <hr />

    <div id="instructions">
      <p>
        Cette image a été généré à partir des informations contenus dans le
        fichier data.json (répertoire assets). Pour chaque jour, le nombre de
        contributions est indiqué en entier. Les dates sans contribution ne sont
        pas inclues dans le fichier.
      </p>
      <h5>Ta mission</h5>
      <ul>
        <li>
          Développer un composant uniquement en VueJS (pas de d3 ou autre)
          permettant d'afficher une vue web similaire à l'image
        </li>
        <li>Tu peux bien entendu développer plusieurs composants si besoin</li>
        <li>
          Au passage de la souris sur une case, on doit voir le nombre exacte de
          contributions
        </li>
        <li>
          L'échelle (en bas à gauche) doit être visible, elle est statique et
          voici les infos à utiliser
          <ul>
            <li>#ededed: 0 contributions</li>
            <li>#acd6f2: 1-9 contributions</li>
            <li>#7fa8c9: 10-19 contributions</li>
            <li>#527ba0: 20-29 contributions</li>
            <li>#264e77: 30+ contributions</li>
          </ul>
        </li>
        <li>
          Il est possible (mais pas indispensable) d'utiliser la library moment
          pour gérer les dates.
        </li>
      </ul>
      <p>Bon courage :)</p>
    </div>
  </div>
</template>

<script>
import commitGrid from '@/components/commitGrid.vue'

export default {
  name: "App",
  components: { commitGrid }
};
</script>

<style lang="sass">
#app
  font-family: Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #808080
  margin-top: 60px

#app p, ul
  text-align: left
</style>