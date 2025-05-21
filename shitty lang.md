```c++
#include <iostream>
#include <vector>
#include <cstring>

class Date {
private:
    int jour, mois, annee;
    
public:
    // Constructeur paramétré
    Date(int j, int m, int a) : jour(j), mois(m), annee(a) {}

    // Surcharge de l'opérateur ==
    bool operator==(const Date& other) const {
        return (jour == other.jour && mois == other.mois && annee == other.annee);
    }

    // Affichage de la date
    void afficher() const {
        std::cout << jour << "/" << mois << "/" << annee;
    }
};
class Client {
private:
    std::string nom;
    std::string prenom;

public:
    // Constructeur paramétré
    Client(std::string n, std::string p) : nom(n), prenom(p) {}

    // Accesseur
    std::string getNom() const { return nom; }
    std::string getPrenom() const { return prenom; }

    // Affichage
    void afficher() const {
        std::cout << "Client: " << nom << " " << prenom;
    }
};
class Transaction {
private:
    Date date;
    float montant;
    std::string type;
    Client client;

public:
    // Constructeur paramétré
    Transaction(Date d, float m, std::string t, Client c)
        : date(d), montant(m), type(t), client(c) {}

    // Surcharge de l'opérateur <<
    friend std::ostream& operator<<(std::ostream& os, const Transaction& t) {
        os << "Date: "; t.date.afficher();
        os << " | Montant: " << t.montant << " | Type: " << t.type << " | ";
        t.client.afficher();
        return os;
    }
};
class JournalComptabilite {
private:
    float solde;
    std::vector<Transaction> transactions;

public:
    // Constructeur paramétré
    JournalComptabilite(float s) : solde(s) {}

    // Surcharge de l'opérateur +
    void operator+(const Transaction& t) {
        transactions.push_back(t);
    }

    // Affichage des transactions
    void afficher() const {
        std::cout << "Solde: " << solde << "\nTransactions:\n";
        for (const auto& t : transactions) {
            std::cout << t << std::endl;
        }
    }

    // Destructeur
    ~JournalComptabilite() {
        transactions.clear();
    }
};

int main() {
    // Création des objets
    Client client1("Dupont", "Jean");
    Date date1(25, 2, 2025);
    Transaction t1(date1, 1500.0, "Crédit", client1);

    // Création du journal et ajout de la transaction
    JournalComptabilite journal(10000.0);
    journal + t1;

    // Affichage des informations
    journal.afficher();

    return 0;
}


```