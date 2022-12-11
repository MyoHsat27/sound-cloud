<template>
  <div class="add-song">
    <button v-if="!showForm" class="btn-link" @click="showForm = true">Add Song</button>
    <form v-if="showForm" @submit.prevent="handleSubmit">
      <div class="cancel-btn" @click="showForm = false">x</div>
      <h4>Add a New Song</h4>
      <input type="text" placeholder="Song Title" required v-model="title">
      <input type="text" placeholder="Artist" required v-model="artist">
      <div v-if="error">{{error}}</div>
      <button v-if="!isPending">Add</button>
      <button v-else disabled>Loading</button>
    </form>
  </div>
</template>

<script>
import {ref} from "vue";
import {v4 as uuidv4} from "uuid";
import useDocument from "@/mixins/useDocument";
export default {
  name: "AddSong",
  props: ["playlist"],
  setup(props) {
    const title = ref("")
    const artist = ref("")
    const showForm = ref(false)
    const {error, isPending, updateDoc} = useDocument('playlists', props.playlist.id)

    const handleSubmit = async () => {
      const newSong = {
        title: title.value,
        artist: artist.value,
        id : uuidv4()
      }
      await updateDoc({
        songs: [...props.playlist.songs, newSong]
      })
      if (!error.value) {
        title.value = ""
        artist.value = ""
      }
    }


    return {title, artist, showForm, handleSubmit, error, isPending}
  }
}
</script>

<style scoped>
.add-song {
  text-align: center;
  margin-top: 40px;
  margin-bottom: 10px;
}

form {
  max-width: 90%;
  text-align: center;
}

.cancel-btn {
  margin-left: auto;
  margin-bottom: 10px;
  background: var(--secondary);
  color: var(--primary);
  display: flex;
  justify-content: center;
  align-items: center;
  width: 35px;
  height: 35px;
  border-radius: 50%;
  text-align: end;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.1s;
}

.cancel-btn:hover {
  color: var(--secondary);
  background: #f2985c
}
</style>