# ArchivioJava3BIT
/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package autoveicoli;


class GestioneParcoAuto {
  // Attributi costanti
  private static final String[] VOCE_MENU =
  { "Acquisto veicolo",
    "Dismissione veicolo",
    "Ricerca (per targa)",
    "Elenco veicoli da revisionare",
    "Termina" };

  
  private static ParcoAuto  parcoAuto;
  private static MainWindow finestra;

  public static void main(String[] args) {

    
    finestra = new MainWindow("Gestione Parco Auto");
    finestra.show();

    
    InputBox richiediDati = new InputBox(finestra, "Inizializzazione");
    int numeroMaxAutomezzi =
        richiediDati.getInteger("Numero massimo di automezzi?");
    parcoAuto = new ParcoAuto(numeroMaxAutomezzi);

    
    boolean ripeti = true;
    while (ripeti) {
      
      ListBox menuPrincipale = new ListBox(finestra, "Menu Principale");
      for (int i = 0; i < VOCE_MENU.length; ++i) {
        menuPrincipale.addItem(VOCE_MENU[i]);
      }
      String scelta = menuPrincipale.getSelectedItem();

      
      if (scelta != null) {
        if (scelta.equals(VOCE_MENU[0])) {
          aggiungiAutomezzo();
        } else if (scelta.equals(VOCE_MENU[1])) {
          eliminaAutomezzo();
        } else if (scelta.equals(VOCE_MENU[2])) {
          ricercaAutomezzoPerTarga();
        } else if (scelta.equals(VOCE_MENU[3])) {
          ricercaAutomezziDaRevisionare();
        } else {
          ripeti = false;
        }
      }
    }
    System.exit(0);
  }

  
  private static void aggiungiAutomezzo()
  {
    
    String[] etichette =
    { "Marca", "Modello", "Targa", "Anno Immatricolazione" };
    MultiInputBox richiediAutomezzo =
        new MultiInputBox(finestra, etichette);

    String[] datiInseriti = richiediAutomezzo.getInputs();
    int annoImmatricolazione =
        Integer.parseInt(datiInseriti[3]);

    
    Automezzo nuovaAuto =
        new Automezzo(datiInseriti[0],
                      datiInseriti[1],
                      datiInseriti[2],
                      annoImmatricolazione,
                      annoImmatricolazione);
    boolean ok = parcoAuto.aggiungi(nuovaAuto);
    MessageBox mb = new MessageBox(finestra);
    if (ok) {
      mb.show("Inserimento effettuato!");
    } else {
      mb.show("Capacità del parco auto esaurita!");
    }
  }

  
  private static void eliminaAutomezzo()
  {
    
    InputBox richiediTarga =
        new InputBox(finestra, "Eliminazione automezzo");

    
    String targa = richiediTarga.getString("Targa del mezzo?");

    
    boolean ok = parcoAuto.elimina(targa);
    MessageBox mb = new MessageBox(finestra);
    if (ok) {
      mb.show("Cancellazione effettuata!");
    } else {
      mb.show("Il mezzo targato " + targa +
              " non è presente nel parco automezzi!");
    }
  }

  /
  private static void ricercaAutomezzoPerTarga()
  {
    
    InputBox richiediTarga =
        new InputBox(finestra, "Ricerca automezzo");

    
    String targa = richiediTarga.getString("Targa del mezzo?");

    
    Automezzo auto = parcoAuto.cercaPerTarga(targa);
    OutputBox ob =
        new OutputBox(finestra, "Risultato della ricerca");
    if (auto != null) {
      visualizzaDatiAuto(auto, ob);
    }else {
      ob.printLine("Il mezzo targato " + targa + " non è presente nel parco automezzi!");
    }
    ob.show();
    ob.waitUntilClose();
  }

  private static void ricercaAutomezziDaRevisionare()
  {
    // Creazione della maschera di ingresso
    InputBox richiediAnno =
        new InputBox(finestra, "Ricerca veicoli da revisionare");

    int anno = richiediAnno.getInteger("Anno corrente?");

    ParcoAuto daRevisionare =
        parcoAuto.cercaAutomezziDaRevisionare(anno);

    OutputBox uscitaDati =
        new OutputBox(finestra, "Veicoli da revisionare");
    if (daRevisionare.restituisciNumeroAutomezzi() > 0) {
      daRevisionare.iniziaScansione();
      Automezzo auto = daRevisionare.prossimoAutomezzo();
      while (auto != null) {
        visualizzaDatiAuto(auto, uscitaDati);
        auto = daRevisionare.prossimoAutomezzo();
      }
    } else {
      uscitaDati.printLine("Non vi sono automezzi da revisionare");
    }
    uscitaDati.show();
    uscitaDati.waitUntilClose();
  }

  
  private static void visualizzaDatiAuto(
      Automezzo auto,
      OutputBox ob)
  {
    ob.printLine("Marca: " + auto.resituisciMarca());
    ob.printLine("Modello: " + auto.restituisciModello());
    ob.printLine("Targa: " + auto.restituisciTarga());
    ob.printLine("Immatricolata nel: " +
                 auto.restituisciAnnoImmatricolazione());
    ob.printLine("Revisionata nel: " +
                 auto.restituisciAnnoRevisione());
  }
}
