<!--
Copyright 2019 Google Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->
<template>
          <table class="table is-bordered" style="width:100%;table-layout: fixed;">
            <tbody>
              <tr v-for="(item, key) in fullEventFiltered" :key="key">
                <td style="width:150px;">{{ key }}</td>
                <td>
                  <span style="white-space:pre-wrap;word-wrap: break-word">{{ item }}</span>
                </td>
                <td style="width:40px;">
                  <span class="icon is-small" style="cursor:pointer;" v-on:click="addFilter(key, item, 'must')"><i class="fas fa-search-plus"></i></span>
                </td>
                <td style="width:40px;">
                  <span class="icon is-small" style="cursor:pointer;" v-on:click="addFilter(key, item, 'must_not')"><i class="fas fa-search-minus"></i></span>
                </td>
              </tr>
            </tbody>
          </table>
</template>

<script>
import ApiClient from '../../utils/RestApiClient'

export default {
  props: ['event'],
  data () {
    return {
      fullEvent: {}
    }
  },
  computed: {
    sketch () {
      return this.$store.state.sketch
    },
    fullEventFiltered () {
      Object.getOwnPropertyNames(this.fullEvent).forEach(key => {
        // Remove internal properties from the UI
        if (key.startsWith('__ts')) {
          delete this.fullEvent[key]
        }
      })
      return this.fullEvent
    }
  },
  methods: {
    getEvent: function () {
      let searchindexId = this.event._index
      let eventId = this.event._id
      ApiClient.getEvent(this.sketch.id, searchindexId, eventId).then((response) => {
        this.fullEvent = response.data.objects
      }).catch((e) => {})
    },
    addFilter: function (field, value, operator) {
      let chip = {
          'field': field,
          'value': value,
          'type': 'term',
          'operator': operator
        }
      this.$emit('addChip', chip)
    }
  },
  created: function () {
    this.getEvent()
  }
}
</script>
