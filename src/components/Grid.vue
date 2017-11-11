<template>
  <div>
    <!-- four rows and four columns -->
<table>
    <tbody>
            <tr>
                <td><tile></tile></td>
                <td><tile></tile></td>
                <td><tile></tile></td>
                <td><tile></tile></td>
            </tr>
                        <tr>
                <td><tile></tile></td>
                <td><tile></tile></td>
                <td><tile></tile></td>
                <td><tile></tile></td>
            </tr>
            <tr>
                <td><tile></tile></td>
                <td><tile></tile></td>
                <td><tile></tile></td>
                <td><tile></tile></td>
            </tr>
            <tr>
                <td><tile></tile></td>
                <td><tile></tile></td>
                <td><tile></tile></td>
                <td><tile></tile></td>
            </tr>
    </tbody>
</table>
    <!-- <tile/><tile/><tile/><tile/>
    <tile/><tile/><tile/><tile/>
    <tile/><tile/><tile/><tile/>
    <tile/><tile/><tile/><tile/> -->
  </div>
</template>
<style>
div {
  width: 600px;
  height: 600px;
}
</style>
<script>
import Tile from "./Tile.vue";
export default {
  components: { Tile }
};
</script>
