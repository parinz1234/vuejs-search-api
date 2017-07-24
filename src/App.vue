<template>
  <div id="app">
    <input type="text">
    <transition-group name="list" tag="p">
      <span v-for="item in searchResult" v-bind:key="item.name" class="list-item">
        {{ item.name }}
      </span>
    </transition-group>
  </div>
</template>

<script>
import Rx from 'rxjs/Rx'
import axios from 'axios'
export default {
  name: 'app',
  data: () => ({
    searchResult: []
  }),
  mounted () {
    let input = document.querySelector('input')
    let observable = Rx.Observable.fromEvent(input, 'input')
    observable
      .map(e => e.target.value)
      .debounceTime(2000)
      .distinctUntilChanged()
      .subscribe(
        (value) => {
          axios.get(`https://api.github.com/search/repositories?q=stars:>1+language:${value}&sort=stars&order=desc&type=Repositories`)
            .then(response => response.data)
            .then(response => {
              this.searchResult = response.items
              console.log(this.searchResult)
            })
            .catch(errors => console.log(errors))
        }
      )
  }
}
</script>

<style>
.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active, .list-leave-active {
  transition: 1s;
}
.list-enter, .list-leave-to /* .list-leave-active for <2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}
</style>
