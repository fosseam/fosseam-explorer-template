# fossExT - Cyto-Config

Das ist eine exzellente Idee und genau der richtige Weg, um den Explorer von einem einfachen Viewer zu einem intelligenten Analyse-Werkzeug zu machen.

Indem wir die Konfiguration in `FOSSEAM_CONFIG` legen, erreichen wir mehrere wichtige Ziele:

1.  **Flexibilität:** Der Explorer ist nicht mehr auf eine einzige Art der Graphen-Darstellung festgelegt.
2.  **Deklarativität:** Der *Benutzer* oder *Entwickler* des Templates beschreibt, *was* die Daten sind, nicht *wie* sie gezeichnet werden sollen.
3.  **Wiederverwendbarkeit:** Wie du richtig sagst, kann diese Konfiguration nicht nur die Cyto-View, sondern auch die (bisher deaktivierte) Document-View und potenziell andere zukünftige Ansichten steuern.

Die `fosseam-config` wird damit zur **zentralen Definition des Datenmodells** für das geladene JSON.

### Konzeptioneller Entwurf für `FOSSEAM_CONFIG`

Lass uns die `cyto`-Sektion in der Konfiguration detailliert entwerfen.

```javascript
window.FOSSEAM_CONFIG = {
  // ... bestehende Konfigurationen
  title: "FOSSEAM Sales Dashboard",
  autoload: 'data.json', // z.B. um eine Beispieldatei direkt zu laden

  // NEUE SEKTION
  cyto: {
    // Der von dir vorgeschlagene Schalter.
    // 'tree': Stellt die JSON-Struktur 1:1 als Baum dar (unser Plan für Phase 1).
    // 'semantic': Nutzt die Konfiguration unten, um einen Beziehungs-Graphen zu erstellen.
    mode: 'semantic', 

    // Definition der "Entitäten" oder "Knoten-Typen".
    // Dies sind die Haupt-Objekte, die als Knoten im Graphen erscheinen sollen.
    entities: [
      {
        path: 'users',      // Wo im JSON finde ich das Array dieser Entitäten?
        idKey: 'id',        // Welches Feld identifiziert eine Entität eindeutig?
        labelKey: 'name',   // Welches Feld soll als Beschriftung des Knotens dienen?
        style: { 'background-color': '#66c2a5' } // Optional: Cyto-Styling für diesen Knotentyp
      },
      {
        path: 'orders',
        idKey: 'orderId',
        labelKey: 'product',
        style: { 'background-color': '#fc8d62' }
      }
    ],

    // Definition der "Beziehungen" oder "Kanten".
    // Beschreibt, wie Entitäten miteinander verbunden sind.
    relations: [
      {
        from: 'orders',     // Name der Quell-Entität (aus 'entities' oben)
        to: 'users',        // Name der Ziel-Entität
        foreignKey: 'userId' // Welches Feld in der 'from'-Entität verweist auf die 'idKey' der 'to'-Entität?
      }
    ]
  }
};
```

### Wie diese Konfiguration die Views steuert

#### 1. Cyto-View

*   **Wenn `cyto.mode === 'tree'` (oder die `cyto` Sektion fehlt):**
    *   Die Cyto-View-Logik durchläuft das gesamte JSON rekursiv und erstellt für jedes Element (Objekt, Array, Wert) einen Knoten und für jede Eltern-Kind-Beziehung eine Kante. Das ist unser "dummer", aber universeller Fallback.
*   **Wenn `cyto.mode === 'semantic'`:**
    *   Die Logik ignoriert die allgemeine Struktur und arbeitet gezielt die Konfiguration ab:
        1.  **Knoten erstellen:** Sie iteriert durch `cyto.entities`. Für jedes `entity`-Objekt (z.B. `users`):
            *   Sie greift auf das Array unter `json[entity.path]` zu (z.B. `json['users']`).
            *   Für jedes Objekt in diesem Array erstellt sie einen Cytoscape-Knoten.
            *   Die ID des Knotens wird aus `obj[entity.idKey]` (z.B. `user.id`) gebildet.
            *   Die Beschriftung wird aus `obj[entity.labelKey]` (z.B. `user.name`) geholt.
        2.  **Kanten erstellen:** Sie iteriert durch `cyto.relations`. Für jede `relation`:
            *   Sie geht die Quell-Entitäten (`orders`) durch.
            *   Für jede `order` holt sie den Wert des `foreignKey` (`order.userId`).
            *   Sie sucht den Knoten der Ziel-Entität (`users`), dessen ID diesem Wert entspricht.
            *   Sie erstellt eine Kante vom `order`-Knoten zum gefundenen `user`-Knoten.

#### 2. Document-View (Zukunftsvision)

Genau hier spielt die Konfiguration ihre Stärke aus! Die bisher deaktivierte "Document-View" könnte jetzt intelligent werden:

*   Anstatt nur rohen Text anzuzeigen, könnte sie die `cyto.entities`-Konfiguration nutzen, um das Dokument zu strukturieren.
*   Sie könnte für jede Entität unter `cyto.entities` eine Überschrift generieren (z.B. `<h2>Benutzer</h2>`, `<h2>Bestellungen</h2>`).
*   Unter jeder Überschrift listet sie die einzelnen Objekte nicht als JSON, sondern in einer schönen Tabelle oder als Karten (Cards) auf, wobei die `idKey` und `labelKey` prominent dargestellt werden.
*   Die `foreignKey`-Beziehungen könnten in klickbare Links umgewandelt werden, die zur entsprechenden verlinkten Entität springen.

**Das Ergebnis:** Die `cyto`-Konfiguration wird zu einer `dataModel`-Konfiguration. Wir könnten sie sogar umbenennen, wenn wir wollten, aber für den Anfang ist `cyto` völlig in Ordnung.

### Fazit und Nächster Schritt

Dein Vorschlag ist perfekt. Er schafft eine klare Trennung zwischen dem generischen Tool und der spezifischen Anwendung. Der Explorer wird damit zu einem Framework, und die `FOSSEAM_CONFIG` ist die "Blaupause" für eine konkrete Daten-Visualisierung.

**Nächster Schritt, wie von dir angedeutet:**

Wir setzen genau diesen Plan um. Wir beginnen mit der **Implementierung der "Cyto-View"**, die auf die `cyto.mode`-Eigenschaft reagiert.

1.  **Phase 1a (UI & Basis-Logik):**
    *   Wir fügen die Cytoscape.js-Bibliothek hinzu.
    *   Wir ergänzen das HTML um den Menüpunkt "Cyto-View" und den Container `<div id="cyto-view"></div>`.
    *   Wir passen das CSS an, damit der View-Container die Höhe korrekt ausfüllt.
    *   Wir implementieren die Klick-Logik, die die Ansichten umschaltet.

2.  **Phase 1b (Tree-Mode):**
    *   Wir implementieren die Logik für `mode: 'tree'`. Dafür können wir die Rekursionslogik der Tree-View als Vorlage nehmen und sie so anpassen, dass sie Cytoscape-Elemente (nodes, edges) ausgibt.

Sobald das steht, haben wir eine funktionierende Basis und können uns an die intelligentere `semantic`-Mode-Implementierung wagen.

Einverstanden?
