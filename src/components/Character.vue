<template>
  <div class="column is-12-mobile is-3-desktop is-3-tablet mb-4">
    <div class="card ch-card">
      <div class="card-header">
        <img
          v-bind:src="character.image"
          v-bind:alt="character.name"
          class="width-100"
        />
      </div>
      <div class="card-content">
        <div style="display: inline">
          <div class="mb-2" style="width: 60%; float: left">
            <h3 class="is-size-5">
              {{
                character.name.length> 12
                  ? character.name.substring(0, 12) + "..."
                  : character.name
              }}
            </h3>
            <small class="mb-1"
              >Episodes | <strong>{{ character.episode.length }}</strong></small
            >
          </div>
          <div class="mb-2" style="width: 40%; float: right">
            <small style="float: right" class="mt-1"
              ><strong class="has-text-success">{{ character.species }}</strong>
            </small>
          </div>
        </div>
        <div style="width: 100%; display: inline-block" class="mt-2">
          <button
            class="button is-success is-rounded is-small"
            @click="showMore(character.id)"
          >
            More Details
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["character"],
  methods: {
    showMore(id) {
      this.$emit("loading", true);
      this.$emit("showModal", id);
    },
  },
};
</script>