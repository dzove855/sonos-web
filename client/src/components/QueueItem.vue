<template>
  <v-list-tile
    avatar class="queue-item"
    :class="queueItemClasses"
    @click="hideMenu"
    @dblclick.native="playTrack"
    @contextmenu.prevent.native="showMenu">
    <v-list-tile-avatar tile size="40px">
      <div class="v-responsive v-image">
        <div v-lazy:background-image="albumArtURL"
        class="background-image" :key="albumArtURL"></div>
      </div>
    </v-list-tile-avatar>
    <v-list-tile-content>
      <v-list-tile-title class="font-weight-bold"
        @mouseover="tooltipOnOverFlow">
        {{ track.title }}
      </v-list-tile-title>
      <div>
        <v-layout>
          <router-link
            @mouseover="tooltipOnOverFlow"
            :to="`/artist/${encodedArtist}`"
            class="item-link text-truncate text-xs-center pa-0">
            {{ track.artist }}
          </router-link>
          <template v-if="track.album">
            <span class="item-link-separator">•</span>
            <router-link
              @mouseover="tooltipOnOverFlow"
              :to="`/album/${encodedAlbum}`"
              class="item-link text-truncate text-xs-center pa-0">
              {{ track.album }}
            </router-link>
          </template>
        </v-layout>
      </div>
    </v-list-tile-content>
    <v-list-tile-action>
      <v-btn icon ripple @click="showMenu">
        <v-icon color="grey lighten-1">more_horiz</v-icon>
      </v-btn>
    </v-list-tile-action>
    <!--
    <v-list-tile-action>
      <v-checkbox class="queue-item-checkbox"
        v-model="checked" color="white" hide-details>
      </v-checkbox>
    </v-list-tile-action>
    -->
  </v-list-tile>
</template>


<script>
import groupsAPI from '@/services/API/groups';
import tooltipOnOverflow from '@/mixins/tooltipOnOverflow';

export default {
  name: 'QueueItem',
  mixins: [tooltipOnOverflow],
  props: {
    track: {
      type: Object,
      required: true,
    },
  },
  methods: {
    playTrack() {
      groupsAPI.playTrackFromQueue(this.activeZoneGroup.id, this.track.queuePosition);
    },
    showMenu(event) {
      this.$emit('show-menu', event, this.track.queuePosition);
    },
    hideMenu() {
      this.$emit('hide-menu');
    },
  },
  computed: {
    activeZoneGroup() {
      return this.$store.getters.activeZoneGroup;
    },
    albumArtURL() {
      return this.track.albumArtURI || this.$store.state.defaultAlbumArtURL;
    },
    active() {
      if (this.activeZoneGroup) {
        return this.activeZoneGroup.track.queuePosition === this.track.queuePosition;
      }
      return false;
    },
    queueItemClasses() {
      let classes = '';
      if (this.active) {
        classes += 'active';
      }
      return classes;
    },
    encodedArtist() {
      return this.$Base64.encodeURI(this.track.artist);
    },
    encodedAlbum() {
      return this.$Base64.encodeURI(this.track.album);
    },
  },
};
</script>

<style>
.queue-item {
  width: 100%;
  -webkit-transition-property: background-color!important;
  transition-property: background-color!important;
  -webkit-transition-duration: .2s!important;
  transition-duration: .2s!important;
  -webkit-transition-timing-function: linear!important;
  transition-timing-function: linear!important;
}
.queue-item .v-list__tile--link:hover {
  background: unset!important;
}
.queue-item.checked {
  background-color: rgba(0,0,0,0.15);
}
.queue-item:hover {
  background-color: rgba(0,0,0,0.25);
}
.queue-item.active {
  background-color: rgba(0,0,0,0.35);
}
.queue-item-checkbox {
  flex: none;
}
.queue-item .v-list__tile__action {
  display: none;
}
.queue-item:hover .v-list__tile__action,
.queue-item.checked .v-list__tile__action {
  display: flex;
}
.queue-item .item-link {
  font-size: 14px;
}
</style>
