<template>
  <div class="main">
    <div class="sidebar">
      <div class="sidebar-block">
        <h1>Sistema de Alerta de Emergencias</h1>
      </div>

      <div class="sidebar-block">
        <h2>1. Escribe tu mensaje</h2>
        <textarea name="" id="" cols="30" rows="10"></textarea>
      </div>

      <div class="sidebar-block">
        <h2>2. Elige Prioridad</h2>

        <div class="select">
          <div class="select-option select-option-low"
            v-bind:class="{'-active': this.priority === 'low'}"
            @click="onChangePriority('low')">
            Bajo
          </div>
          <div class="select-option select-option-medium"
            v-bind:class="{'-active': this.priority === 'medium'}"
            @click="onChangePriority('medium')">
            Moderado
          </div>
          <div class="select-option select-option-high"
            v-bind:class="{'-active': this.priority === 'high'}"
            @click="onChangePriority('high')">
            Grave
          </div>
        </div>
      </div>

      <div class="sidebar-block">
        <h2>3. Región</h2>
        Lorem ipsum dolor sit amet, consectetur adipisicing elit. Saepe sapiente deleniti architecto minima quibusdam maxime, blanditiis sequi doloremque fuga veritatis! 
      </div>

        
      <div class="sidebar-block"> 
        <div class="button" @click="this.sendMessage">
          Enviar
        </div>
      </div>
    </div>

    <div class="map">
    </div>

    <div class="progress">
      <h1>{{this.totalPlaces - this.remainingPlaces - this.places.length}}/{{this.totalPlaces - this.remainingPlaces}}</h1>
      <div>aceptadas/notificadas</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      map: null,
      drawingManager: null,
      places: [],

      totalPlaces: 20,
      remainingPlaces: 20,

      priority: "low"
    }
  },

  mounted() {
    this.map = new google.maps.Map(this.$el.querySelector('.map'), {
      center: {
        "lat": 20.6184601,
        "lng": -103.4982431
      },
      scrollwheel: false,
      zoom: 8,
      disableDefaultUI: true,
      styles: [{"featureType":"all","elementType":"geometry","stylers":[{"color":"#d12727"},{"visibility":"on"}]},{"featureType":"all","elementType":"geometry.fill","stylers":[{"color":"#d14545"},{"visibility":"on"}]},{"featureType":"all","elementType":"labels.text.fill","stylers":[{"saturation":36},{"color":"#333333"},{"lightness":40}]},{"featureType":"all","elementType":"labels.text.stroke","stylers":[{"visibility":"on"},{"color":"#ffffff"},{"lightness":16}]},{"featureType":"all","elementType":"labels.icon","stylers":[{"visibility":"off"}]},{"featureType":"administrative","elementType":"geometry.fill","stylers":[{"color":"#fefefe"},{"lightness":20}]},{"featureType":"administrative","elementType":"geometry.stroke","stylers":[{"color":"#fefefe"},{"lightness":17},{"weight":1.2}]},{"featureType":"administrative.province","elementType":"geometry.fill","stylers":[{"visibility":"on"},{"color":"#f11212"},{"saturation":"76"}]},{"featureType":"administrative.province","elementType":"geometry.stroke","stylers":[{"visibility":"on"},{"color":"#00b579"}]},{"featureType":"landscape","elementType":"geometry","stylers":[{"color":"#f5f5f5"},{"lightness":20}]},{"featureType":"poi","elementType":"geometry","stylers":[{"color":"#f5f5f5"},{"lightness":21}]},{"featureType":"poi.park","elementType":"geometry","stylers":[{"color":"#dedede"},{"lightness":21}]},{"featureType":"road.highway","elementType":"geometry.fill","stylers":[{"color":"#ffffff"},{"lightness":17}]},{"featureType":"road.highway","elementType":"geometry.stroke","stylers":[{"color":"#ffffff"},{"lightness":29},{"weight":0.2}]},{"featureType":"road.arterial","elementType":"geometry","stylers":[{"color":"#ffffff"},{"lightness":18}]},{"featureType":"road.local","elementType":"geometry","stylers":[{"color":"#ffffff"},{"lightness":16}]},{"featureType":"transit","elementType":"geometry","stylers":[{"color":"#f2f2f2"},{"lightness":19}]},{"featureType":"water","elementType":"geometry","stylers":[{"color":"#e9e9e9"},{"lightness":17}]}]
    });

    this.drawingManager = new google.maps.drawing.DrawingManager({
      drawingMode: google.maps.drawing.OverlayType.MARKER,
      drawingControl: true,
      drawingControlOptions: {
        position: google.maps.ControlPosition.TOP_CENTER,
        drawingModes: ['circle', 'polyline', 'rectangle']
      },
      circleOptions: {
        fillColor: '#3498DB',
        strokeColor: '#2980B9',
        fillOpacity: 0.1,
        strokeOpacity: 0.3,
        strokeWeight: 2,
        clickable: false,
        editable: true,
        zIndex: 1
      }
    });
    this.drawingManager.setMap(this.map);

    this.clearMessages();
  },

  methods: {
    sendMessage() {
      setTimeout(() => {
        const placeLocation = {
          "lat": 20.6184601 + Math.random() * 1.5,
          "lng": -103.4982431 + Math.random() * 1.5
        };

        this.addCircle(placeLocation, 0, '#E67E22', 2500);

        this.places.push(placeLocation);

        if (this.remainingPlaces-- > 0) {
          this.sendMessage();
        }
      }, Math.random() * 600)
    },
    clearMessages() {
      setTimeout(() => {
        if (this.places.length) {
          const toRemove = this.places.pop();
          this.addCircle(toRemove, 0, '#27AE60', 3000);
        }

        this.clearMessages();
      }, Math.random() * 3000)
    },
    addCircle(center, _id, color, size) {
      new google.maps.Circle({
        strokeColor: '#fff',
        strokeOpacity: 1,
        strokeWeight: 0,
        fillColor: color,
        fillOpacity: 1,
        map: this.map,
        center: center,
        radius: size,
      })
    },
    onChangePriority(newPriority) {
      this.priority = newPriority;
    }
  }
}
</script>

<style lang="scss">
html {
  box-sizing: border-box;
}
*, *:before, *:after {
  box-sizing: inherit;
}
* {
  margin: 0;
  padding: 0;
  position: relative;
}

.main {
  font-family: -apple-system, BlinkMacSystemFont, sans-serif;
  line-height: 1.2;
  display: flex;
  height: 100vh;
  width: 100vw;
  flex-align: stretch;
}

.sidebar {
  background: #ECF0F1;
  color: #333;
  font-weight: 300;
  width: 24rem;
  padding: 1rem;
}
.sidebar-block {
  padding: 1rem;
}

.map {
  flex: 1;
}

h1, h2 {
  color: #000;
}

.button {
  background: #3498DB;
  color: #fff;
  text-transform: uppercase;
  font-weight: 700;
  font-size: 2rem;
  line-height: 1;
  padding: 1rem;
  text-align: center;

  &:hover {
    background: lighten(#3498DB, 10%);
    cursor: pointer;
  }

  &:active {
    top: 1px;
    background: darken(#3498DB, 10%);
    cursor: pointer;
  }
}

textarea {
  padding: 1rem;
  border: 0;
  margin: 1rem 0;
  display: block;
  width: 100%;
}

.progress {
  text-align: right;
  position: fixed;
  bottom: 3rem;
  right: 3rem;

  h1 {
    font-size: 4rem;
  }
}

.select {
  padding: 1rem 0;
  font-size: 1.5rem;
}
.select-option {
  font-weight: 400;
  cursor: pointer;
  padding: 0.2rem 0;

  &::before {
    content: "";
    position: relative;
    display: inline-block;
    height: 0.5rem;
    width: 0.5rem;
    border-radius: 1rem;
    border: 1px solid #000;
    margin-right: 0.25em;
    top: -0.25rem;
  }

  &.-active {
    &::before {
      background: #000;
    }
  }
}
.select-option.-active {
  font-weight: 700;
}
</style>
