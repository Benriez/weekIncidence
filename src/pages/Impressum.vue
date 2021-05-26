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
    <h3 style="color: #dfdfdf;">Impressum</h3>
    <p style="color: #dfdfdf; font-size: 0.9rem">
        <b>Angaben gemäß § 5 TMG</b>
        <br>
        <br>Paul Robertz
        <br>Darmstädter Straße 185
        <br>64625 Bensheim
        <br>
        <br><b>Kontakt</b>
        <br>Telefon: 015115260725
        <br>E-Mail: inzidenzdeutschland@gmail.com
        <br>
        <br><b>Haftung für Inhalte</b>
        <br>Als Diensteanbieter sind wir gemäß § 7 Abs.1 TMG für eigene Inhalte auf diesen Seiten nach den
        allgemeinen Gesetzen verantwortlich. Nach §§ 8 bis 10 TMG sind wir als Diensteanbieter jedoch nicht
        verpflichtet, übermittelte oder gespeicherte fremde Informationen zu überwachen oder nach Umständen zu
        forschen, die auf eine rechtswidrige Tätigkeit hinweisen.
        <br>Verpflichtungen zur Entfernung oder Sperrung der Nutzung von Informationen nach den allgemeinen
        Gesetzen bleiben hiervon unberührt. Eine diesbezügliche Haftung ist jedoch erst ab dem Zeitpunkt der
        Kenntnis einer konkreten Rechtsverletzung möglich. Bei Bekanntwerden von entsprechenden
        Rechtsverletzungen werden wir diese Inhalte umgehend entfernen.
        <br>
        <br><b>Haftung für Links</b>
        <br>Unser Angebot enthält Links zu externen Websites Dritter, auf deren Inhalte wir keinen Einfluss haben.
        Deshalb können wir für diese fremden Inhalte auch keine Gewähr übernehmen. Für die Inhalte der
        verlinkten Seiten ist stets der jeweilige Anbieter oder Betreiber der Seiten verantwortlich. Die verlinkten
        Seiten wurden zum Zeitpunkt der Verlinkung auf mögliche Rechtsverstöße überprüft. Rechtswidrige Inhalte
        waren zum Zeitpunkt der Verlinkung nicht erkennbar.
        <br>Eine permanente inhaltliche Kontrolle der verlinkten Seiten ist jedoch ohne konkrete Anhaltspunkte einer
        Rechtsverletzung nicht zumutbar. Bei Bekanntwerden von Rechtsverletzungen werden wir derartige Links
        umgehend entfernen.
        <br>
        <br><b>Urheberrecht</b>
        <br>Die durch die Seitenbetreiber erstellten Inhalte und Werke auf diesen Seiten unterliegen dem deutschen
        Urheberrecht. Die Vervielfältigung, Bearbeitung, Verbreitung und jede Art der Verwertung außerhalb der
        Grenzen des Urheberrechtes bedürfen der schriftlichen Zustimmung des jeweiligen Autors bzw. Erstellers.
        Downloads und Kopien dieser Seite sind nur für den privaten, nicht kommerziellen Gebrauch gestattet.
        <br>Soweit die Inhalte auf dieser Seite nicht vom Betreiber erstellt wurden, werden die Urheberrechte Dritter
        beachtet. Insbesondere werden Inhalte Dritter als solche gekennzeichnet. Sollten Sie trotzdem auf eine
        Urheberrechtsverletzung aufmerksam werden, bitten wir um einen entsprechenden Hinweis. Bei
        Bekanntwerden von Rechtsverletzungen werden wir derartige Inhalte umgehend entfernen.
        <br>
        <br>Quelle:
        <a class="link"  href="https://www.e-recht24.de/">eRecht24</a>

    </p>
  </q-page>
</template>

<script>
export default {
    name: 'impressum',
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
  
  .link
    padding-bottom: 2rem
    text-decoration: none
    color: #cfcfcf
</style>