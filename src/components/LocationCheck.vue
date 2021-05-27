<template>
    <q-banner inline-actions class="text-white bg-red turn-on-location" @click="TurnOnLocation">
      Bitte aktivieren Sie zuerst die Standortfreigabe auf Ihrem Gerät.
      <template v-slot:action>
        <q-btn flat color="white" label="Verstanden" />
      </template>
    </q-banner>
</template>

<script>

export default {
  name: 'LocationCheck',
  methods: {
        TurnOnLocation(){
            var x = document.getElementsByClassName("turn-on-location")[0]
            x.style.display = "none"
        }
  }
}
</script>
