<template>

  <div class="flex">

    <div class="w-1/3 h-screen sticky top-0 bg-gray-200">
      <Settings v-model="settings"/>
    </div>

    <div class="bg-white p-8 h-screen flex-grow overflow-y-auto">

      <div v-if="settings.validToken && settings.env">

        <Crumbs :selected="selected" @reset="reset" />

        <ListItems
          v-show="!selected.state"
          :items="states">
            <div
              slot-scope="each"
              @click="setState(each.item.state_abbr)">
                {{each.item.state}}
            </div>
        </ListItems>


        <ListItems
          v-show="!selected.county"
          :items="counties">
            <div
              slot-scope="each"
              @click="setCounty(each.item.county)">
                {{each.item.county}}
            </div>
        </ListItems>


        <ListItems
          :items="stories">
            <div
              slot-scope="each"
              @click="log(each.item)">
                {{each.item.level}} - {{each.item.geo.name}} - {{each.item.type}} ({{each.item.price_range.name}})
            </div>
        </ListItems>
      </div>

      <div class="h-full flex flex-col items-center justify-center font-bold text-red-600" v-else>
        <div v-if="!settings.env.length">Select Environment</div>
        <div v-if="!settings.token">Enter API Token</div>
        <div v-if="settings.token && !settings.validToken">Invalid API Token</div>
      </div>
    </div>

  </div>
</template>

<script>

import Crumbs from './Crumbs.vue';
import ListItems from './ListItems.vue';
import Settings from './Settings.vue';

const queryParams = params => Object.keys(params)
  .map(k => `${encodeURIComponent(k)}=${encodeURIComponent(params[k])}`)
  .join('&');

const initialState = (settings = []) => ({

  ...settings,

  selected: {
    state: null,
    county: null,
  },

  stories: [],
  states: [],
  counties: [],
});

export default {

  components: {
    Settings,
    Crumbs,
    ListItems,
  },

  data: () => initialState({
    settings: {
      env: '',
      token: '',
      hasStories: true,
      topStories: false,
      validToken: false,
      logs: [],
    },
  }),

  watch: {
    'settings.hasStories': {
      handler() {
        this.reset();
      },
    },
    'settings.topStories': {
      handler() {
        this.reset();
      },
    },
    'settings.token': {
      handler() {
        this.reset();
      },
    },
    'settings.env': {
      handler() {
        this.reset();
      },
    },
  },

  mounted() {
    this.reset();
  },

  methods: {
    log(log) {
      this.settings.logs.push(log);
    },

    reset() {
      Object.assign(this.$data, initialState());
      this.log('Reset');
      this.getStates();
    },

    setState(state) {
      this.selected.state = state;
      this.getCounties();
    },

    setCounty(county) {
      this.selected.county = county;
      this.getStories();
    },

    async getStories() {
      const stories = await this.call('stories', {
        'filter[county]': this.selected.county,
        'filter[state]': this.selected.state,
        top_stories: this.settings.topStories ? 1 : 0,
        per_page: 9999,
      });

      if (stories.data.length === 0) {
        this.log('No stories found.');
      }

      this.stories = stories.data;
    },
    async getCounties() {
      const counties = await this.call('geos/counties', {
        'filter[state]': this.selected.state,
        has_stories: this.settings.hasStories ? 1 : 0,
      });
      this.counties = counties.data;
    },
    async getStates() {
      const states = await this.call('geos/states', {
        has_stories: this.settings.hasStories ? 1 : 0,
      });
      this.states = states.data;
    },
    async call(endpoint, params) {
      if (!this.settings.env) {
        this.log('Please select environment');
        return {};
      }

      let url = `${this.settings.env}/api/v0/${endpoint}`;

      if (params) {
        url += (url.indexOf('?') === -1 ? '?' : '&') + queryParams(params);
      }

      this.log(`Endpoint call: ${url}`);

      const res = await fetch(url, {
        headers: {
          Accept: 'application/json',
          Authorization: `Bearer ${this.settings.token}`,
        },
      });

      if (res.status === 200) {
        this.settings.validToken = true;
      }

      if (res.status === 401) {
        this.settings.validToken = false;
        this.log('Invalid API Token');
      }

      const json = await res.json();
      return json;
    },
  },

};
</script>
