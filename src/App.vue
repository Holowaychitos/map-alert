<template>
  <div class="main">
    <div class="sidebar">
      <div class="sidebar-block">
        <h1 class="logo">
          <img src="./assets/sea-logo.svg" alt='Sistema Educativo '>
        </h1>
      </div>

      <div class="sidebar-block">
        <h2>1. Escribe tu mensaje</h2>
        <textarea name="" id="mess" cols="30" rows="10"></textarea>
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
        <div class="button" @click="this.po" :disabled="sent">
          {{sent ? 'Enviado' : 'Enviar'}}
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
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>

<script>

//const fetch = require('fetch')

import axios from 'axios';

export default {
  name: 'app',
  data () {
    return {
      map: null,
      drawingManager: null,
      places: [],

      totalPlaces: 20,
      remainingPlaces: 20,

      priority: "low",

      sent: false
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
      drawingMode: google.maps.drawing.OverlayType.CIRCLE,
      drawingControl: true,
      drawingControlOptions: {
        position: google.maps.ControlPosition.TOP_CENTER,
        drawingModes: ['circle']
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
    po() {

      axios.post('http://localhost:3000/notification', {
        priority: 'HIGH',
        value: document.getElementById('mess').value
      })
      .then(function (response) {
        console.log(response);
      })
      .catch(function (error) {
        console.log(error);
      });



        this.sendMessage()
    },

    sendMessage() {

      this.sent = true;
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
@import 'sass/main.scss'
</style>
