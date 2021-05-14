<template>
  <v-app>
    <infinite-loading
      spinner="circles"
      direction="top"
      @infinite="infiniteHandler"
    >
      <div slot="no-more">No more persons</div>
      <div slot="no-results">No results persons</div>
      <div slot="error" slot-scope="{ trigger }">
        Error consult, click <a href="javascript:;" @click="trigger">here</a> to
        retry
      </div>
    </infinite-loading>
    <h1>Random User</h1>
    <div class="person" v-for="person in persons" :key="person.email">
      <div class="left">
        <img :src="person.picture.large" />
      </div>
      <div class="right">
        <p>{{ person.name.first }} {{ person.name.last }}</p>
        <ul>
          <li><strong>Birthday:</strong> {{ person.dob.date }}</li>
          <li class="text-capitalize">
            <strong>Location:</strong> {{ person.location.city }},
            {{ person.location.state }}
          </li>
        </ul>
      </div>
    </div>
  </v-app>
</template>

<script>
import axios from 'axios';
import InfiniteLoading from 'vue-infinite-loading';

export default {
  name: 'App',
  components: {
    InfiniteLoading,
  },
  data() {
    return {
      persons: [],
      page: 1,
    };
  },
  methods: {
    async infiniteHandler($state) {
      try {
        const response = await axios.get(`https://randomuser.me/api`);
        setTimeout(() => {
          if (this.page !== 5) {
            this.persons.unshift(response.data.results[0]);
            this.page += 1;
            $state.loaded();
          } else {
            $state.complete();
          }
        }, 500);
      } catch (err) {
        $state.error();
      }
    },
  },
};
</script>

<style lang="scss">
.person {
  background: #ccc;
  border-radius: 2px;
  width: 20%;
  margin: 0 auto 15px auto;
  padding: 15px;

  img {
    width: 100%;
    height: auto;
    border-radius: 2px;
  }

  p:first-child {
    text-transform: capitalize;
    font-size: 2rem;
    font-weight: 900;
  }

  .text-capitalize {
    text-transform: capitalize;
  }
}
</style>
