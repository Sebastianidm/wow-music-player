<template>
  <div class="music-player-container">
    <div class="card-container">
      <div class="card has-text-centered">
        <div
          class="card-content"
          style="
            background: linear-gradient(to right, #0c1e0c, #097f09);
            color: #ffd100;
            border-radius: 10px;
          "
        >
          <!-- Contenido del reproductor de música -->

          <div class="song-info" style="margin-bottom: 20px; max-width: 200px">
            <h2
              class="title is-4"
              style="
                color: #ffd100;
                overflow: hidden;
                white-space: nowrap;
                text-overflow: ellipsis;
              "
            >
              {{ currentSong.name }}
            </h2>
            <p
              class="subtitle is-6"
              style="
                color: #ffd100;
                overflow: hidden;
                white-space: nowrap;
                text-overflow: ellipsis;
              "
            >
              {{ currentSong.artist }}
            </p>
          </div>
          <div class="album-cover">
            <img
              src="../../public/img/caratula.png"
              alt="Album Cover"
              style="width: 150px; height: 150px; border-radius: 50%"
            />
          </div>
          <div class="playback-controls">
            <div class="progress-bar" style="margin: 12px 0">
              <progress
                class="progress is-success"
                :value="position"
                :max="duration"
              ></progress>
            </div>

            <div class="buttons has-addons is-centered">
              <button
                class="button is-success custom-button"
                @click="previousSong"
              >
                Anterior
              </button>
              <button
                class="button is-primary custom-button"
                @click="togglePlayPause"
              >
                {{ isPlaying ? "Pausar" : "Reproducir" }}
              </button>
              <button class="button is-success custom-button" @click="nextSong">
                Siguiente
              </button>
            </div>

            <div class="buttons is-centered">
              <button class="button is-danger custom-button mt-3" @click="stop">
                Detener
              </button>
            </div>
          </div>

          <audio
            ref="audio"
            :src="currentSong.audio"
            @timeupdate="updatePosition"
            @ended="audioEnded"
          ></audio>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  width: 100%; /* Ancho relativo de la tarjeta */
  height: 60vh; /* Altura relativa de la tarjeta */
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  overflow: hidden;
}

.buttons {
  margin-top: 10px; /* Espaciado superior para los botones */
}

.progress-bar {
  margin: 12px 0;
}

.card-content {
  flex-grow: 1; /* Permite que el contenido ocupe el espacio restante */
  padding: 20px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.music-player-container {
  width: 100%;
  max-width: 1000px;
  margin: 0 10; /* Centrar el contenedor en pantallas más grandes */
  padding: 6px;
  background-color: #2ecc71; /* Fondo verde */
  border-radius: 10px; /* Bordes redondeados */
}

@media only screen and (max-width: 600px) {
  .card {
    height: auto;
    display: flex;
    flex-direction: column;
    align-items: center; /* Centra los elementos horizontalmente */
    justify-content: center; /* Centra los elementos verticalmente */
  }

  .card-content {
    flex-grow: 1;
    padding: 20px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    width: 100%;
    height: 100%;
  }
}

.wow-theme {
  background-size: contain; /* Ajustar el tamaño del fondo */
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
}

/* Nueva paleta de colores inspirada en Legion */
.has-text-centered {
  background: linear-gradient(to right, #0c1e0c, #097f09);
  color: #ffd100;
}

.song-info h2,
.song-info p {
  color: #00ff00; /* Verde eléctrico */
}

.progress-bar progress {
  background-color: #00ff00; /* Verde eléctrico */
}

.buttons button {
  background-color: #097f09; /* Verde oscuro */
  color: #ffd100;
}

.buttons button:hover {
  background-color: #00ff00; /* Cambio a verde eléctrico al pasar el ratón */
}

.album-cover {
  margin-top: 20px;
  text-align: center;
}
</style>

<script>
export default {
  data() {
    return {
      position: 0,
      duration: 0,
      isPlaying: false,
      currentSongIndex: 0,
      currentSong: null,
      playlist: [
        {
          name: "Kingdoms Will Burn",
          artist: "Blizzard",
          audio: "/music/01. Kingdoms Will Burn.mp3",
        },
        {
          name: "Relentless",
          artist: "Blizzard",
          audio: "/music/02. Relentless.mp3",
        },
        {
          name: "The Tomb Of Sargeras",
          artist: "Blizzard",
          audio: "/music/04. The Tomb Of Sargeras.mp3",
        },
      ],
    };
  },

  created() {
    this.currentSong = this.playlist[0];
  },

  methods: {
    async togglePlayPause() {
      if (this.isPlaying) {
        await this.$refs.audio.pause();
      } else {
        // Solo inicia la reproducción si no estamos cambiando de canción
        if (this.$refs.audio.src !== this.currentSong.audio) {
          this.$refs.audio.src = this.currentSong.audio;
          await this.$refs.audio.play();
        } else {
          // Si estamos cambiando de canción y está pausada, simplemente reanudamos la reproducción
          await this.$refs.audio.play();
        }
      }
      // Cambiar el texto del botón después de cambiar el estado de reproducción
      this.isPlaying = !this.isPlaying;
    },
    stop() {
      this.$refs.audio.pause();
      this.$refs.audio.currentTime = 0;
      this.isPlaying = false;
    },

    updatePosition(event) {
      const currentTime = event.target.currentTime;
      const newDuration = event.target.duration;

      if (!isNaN(newDuration) && isFinite(newDuration)) {
        this.duration = newDuration;
      }

      this.position = currentTime;
    },

    changeSong(index) {
      this.currentSongIndex = index;
      this.currentSong = this.playlist[index];
      this.togglePlayPause();
    },

    previousSong() {
      // Pausa la reproducción actual y reinicia la posición
      this.$refs.audio.pause();
      this.$refs.audio.currentTime = 0;

      // Cambia la canción solo si no es la primera de la lista
      if (this.currentSongIndex > 0) {
        this.currentSongIndex--;
        this.currentSong = this.playlist[this.currentSongIndex];

        // Espera hasta que el navegador indique que puede reproducir la nueva fuente de audio
        this.$refs.audio.oncanplaythrough = () => {
          this.$refs.audio.play();
          this.isPlaying = true;
          this.$refs.audio.oncanplaythrough = null; // Limpiar el evento después de usarlo
        };

        this.$refs.audio.src = this.currentSong.audio;
      } else {
        // Si es la primera canción, detén la reproducción
        this.stop();
      }
    },
    nextSong() {
      // Pausa la reproducción actual y reinicia la posición
      this.$refs.audio.pause();
      this.$refs.audio.currentTime = 0;

      // Cambia la canción solo si no es la última de la lista
      if (this.currentSongIndex < this.playlist.length - 1) {
        this.currentSongIndex++;
        this.currentSong = this.playlist[this.currentSongIndex];

        // Espera hasta que el navegador indique que puede reproducir la nueva fuente de audio
        this.$refs.audio.oncanplaythrough = () => {
          this.$refs.audio.play();
          this.isPlaying = true;
          this.$refs.audio.oncanplaythrough = null; // Limpiar el evento después de usarlo
        };

        this.$refs.audio.src = this.currentSong.audio;
      } else {
        // Si es la última canción, detén la reproducción
        this.stop();
      }
    },
    audioEnded() {
      this.isPlaying = false;
      this.position = 0;

      // Pausa la reproducción actual y reinicia la posición
      this.$refs.audio.pause();
      this.$refs.audio.currentTime = 0;

      // Cambia la canción solo si no es la última de la lista
      if (this.currentSongIndex < this.playlist.length - 1) {
        this.currentSongIndex++;
        this.currentSong = this.playlist[this.currentSongIndex];

        // Espera hasta que el navegador indique que puede reproducir la nueva fuente de audio
        this.$refs.audio.oncanplaythrough = () => {
          this.$refs.audio.play();
          this.isPlaying = true;
          this.$refs.audio.oncanplaythrough = null; // Limpiar el evento después de usarlo
        };

        this.$refs.audio.src = this.currentSong.audio;
      } else {
        // Si es la última canción, detén la reproducción
        this.stop();
      }
    },
  },
};
</script>
