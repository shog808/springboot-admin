<!--
  - Copyright 2014-2019 the original author or authors.
  -
  - Licensed under the Apache License, Version 2.0 (the "License");
  - you may not use this file except in compliance with the License.
  - You may obtain a copy of the License at
  -
  -     http://www.apache.org/licenses/LICENSE-2.0
  -
  - Unless required by applicable law or agreed to in writing, software
  - distributed under the License is distributed on an "AS IS" BASIS,
  - WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  - See the License for the specific language governing permissions and
  - limitations under the License.
  -->

<template>
  <sba-panel :title="$t('instances.details.health.title')">
    <template v-slot:actions>
      <router-link
        :to="{ name: 'journal', query: { 'instanceId' : instance.id } }"
        class="button icon-button"
      >
        <font-awesome-icon icon="history" />
      </router-link>
    </template>
    <div>
      <sba-alert v-if="error" :error="error" :title="$t('instances.details.health.fetch_failed')" severity="WARN" />
      <health-details :health="health" name="Instance" />
    </div>
  </sba-panel>
</template>

<script>
import Instance from '@/services/instance';
import healthDetails from './health-details';

export default {
  props: {
    instance: {
      type: Instance,
      required: true
    }
  },
  components: {healthDetails},
  data: () => ({
    error: null,
    liveHealth: null,
  }),
  created() {
    this.fetchHealth();
  },
  computed: {
    health() {
      return this.liveHealth || this.instance.statusInfo;
    }
  },
  methods: {
    async fetchHealth() {
      this.error = null;
      try {
        const res = await this.instance.fetchHealth();
        this.liveHealth = res.data;
      } catch (error) {
        console.warn('Fetching live health failed:', error);
        this.error = error;
      }
    }
  }
}
</script>
