<template>
  <q-page class="flex column constrain-more q-pa-md">
    <q-header elevated class="text-white" style="background: #000">
      <q-toolbar>
        <q-toolbar-title class="col q-pt-lg q-px-md">
          <q-item 
            to="/">
              <q-icon class="homeIcon" name="home" style="font-size: 1.5em; color: #dfdfdf; "/>
          </q-item>
        </q-toolbar-title>
      </q-toolbar>
    </q-header>
    <h3 style="color: #dfdfdf;">Datenschutz</h3>
    <p style="color: #dfdfdf; font-size: 0.9rem">
        <b>Datenschutzerklärung</b>
        <br>
        Letztes Update: 07.05.2021
        <br>
        <br>
        In dieser Datenschutzerklärung legen wir dar, wie wir mit Nutzerdaten verfahren und wofür
        diese genutzt werden.
        Wir benutzen die gesammelten Nutzerdaten, um den Dienst unserer Applikation
        gewährleisten zu können. Bei Nutzung unserer Applikation stimmen Nutzer der Sammlung
        und Nutzung ihrer persönlichen Daten zu, so wie es in dieser Datenschutzerklärung
        dargelegt wird.
        <br>
        <br>
        <b>Standortdaten</b>
        <br>
        Wir benötigen den Zugriff auf den Standort Ihres Gerätes. Bei einer Anfrage erheben wir den
        aktuellen Standort unserer Nutzer über GPS, um ihnen schnell Informationen zu ihrer
        unmittelbaren Umgebung geben zu können. Daten zu diesem Standort werden nur für die
        Bearbeitung der Anfrage genutzt. Die Übertragung Ihrer Standortdaten erfolgt über eine
        verschlüsselte Verbindung. Ihre Standortdaten werden nach Beendigung der Nutzeranfrage
        gelöscht und können somit von niemandem mehr aufgerufen werden.
        <br>
        <br>
        <b>Links zu anderen Webseiten</b>
        <br>
        Unsere App enthält einen Link zu einer anderen Webseite, die nicht von uns betrieben wird.
        Wenn Nutzer diesen Link anklicken, werden sie zu der zugehörigen Webseite weitergeleitet.
        Wir empfehlen auch, die Datenschutzerklärung der besagten Webseite durchzulesen.
        Wir haben keine Kontrolle und tragen keine Verantwortung über Inhalt, Datenschutz und
        Vorgehensweisen, die auf der verlinkten Webseite dargestellt und durchgeführt werden.
        <br>
        <br>
        <b>Änderungen dieser Datenschutzerklärung</b>
        <br>
        Wenn es weitere Updates unserer Applikation verlangen, nehmen wir uns die Freiheit, die
        Datenschutzerklärung zu aktualisieren. Nutzer werden über Änderungen durch unsere
        jedem Update beiliegenden Notizen unterrichtet.
        Wir empfehlen Nutzern, die Datenschutzerklärung regelmäßig durchzulesen und nach
        Änderungen Ausschau zu halten. Änderungen dieser Datenschutzerklärung treten in Kraft,
        sobald diese auf diese Seite gestellt werden.
        <br>
        <br>
        <b>Kontaktiere uns</b>
        <br>
        Wenn Fragen über diese Datenschutzerklärung, schreibe uns bitte eine E-Mail an:
        <br>
        <br>
        inzidenzdeutschland@gmail.com</p>







  </q-page>
</template>

<script>
export default {
    name: 'datenschtuz',
    data() {
        return {
     
        }
    }
}


</script>



<style lang="sass">
  .q-page
    background: linear-gradient(to top, #712c57, #000000)
    &.bg-night
      background: linear-gradient(to bottom, #712c57, #000000)
    &.bg-day
      background: linear-gradient(to top, #712c57, #000000)
  .degree
    top: -44px
</style>