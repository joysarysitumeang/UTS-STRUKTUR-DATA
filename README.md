# UTS-STRUKTUR-DATA

#include <iostream>
#include <string>
#include <iomanip>
#include <limits>
using namespace std;

// DATA MAHASISWA
struct Mahasiswa {
    string nim, nama;
    double tugas, kehadiran, uts, uas;
    double mutuAngka;
    char mutuHuruf;
};

// NODE LINKED LIST
struct Node {
    Mahasiswa data;
    Node *next;
    Node *prev;

    Node(Mahasiswa m) {
        data = m;
        next = nullptr;
        prev = nullptr;
    }
};

// CLASS LINKED LIST
class LinkedList {
private:
    Node *head, *tail;
    int jumlah;
    int jenis;

    Node* cariNode(int posisi) {
        Node *p = head;

        for (int i = 1; i < posisi; i++) {
            p = p->next;
        }

        return p;
    }

    void perbaikiLink() {
        if (jumlah == 0) {
            head = tail = nullptr;
            return;
        }

        Node *p = head;
        Node *sebelumnya = nullptr;

        for (int i = 0; i < jumlah; i++) {
            if (jenis == 2)
                p->prev = sebelumnya;
            else
                p->prev = nullptr;

            sebelumnya = p;

            if (i < jumlah - 1)
                p = p->next;
        }

        tail = sebelumnya;

        if (jenis == 3)
            tail->next = head;
        else
            tail->next = nullptr;
    }

public:
    LinkedList(int tipe) {
        head = nullptr;
        tail = nullptr;
        jumlah = 0;
        jenis = tipe;
    }

    ~LinkedList() {
        while (jumlah > 0) {
            Node *hapus = head;
            head = head->next;
            delete hapus;
            jumlah--;
        }
    }

    int ukuran() {
        return jumlah;
    }

    // MENYISIPKAN DATA
    void sisip(int posisi, Mahasiswa m) {
        if (posisi < 1 || posisi > jumlah + 1) {
            cout << "Posisi tidak valid!\n";
            return;
        }

        Node *baru = new Node(m);

        if (jumlah == 0) {
            head = tail = baru;
        }
        else if (posisi == 1) {
            baru->next = head;
            head = baru;
        }
        else {
            Node *p = cariNode(posisi - 1);

            baru->next = p->next;
            p->next = baru;

            if (posisi == jumlah + 1)
                tail = baru;
        }

        jumlah++;
        perbaikiLink();

        cout << "Data berhasil disisipkan!\n";
    }

    // MENGHAPUS DATA
    void hapus(int posisi) {
        if (posisi < 1 || posisi > jumlah) {
            cout << "Posisi tidak valid!\n";
            return;
        }

        Node *target;

        if (posisi == 1) {
            target = head;
            head = head->next;
        }
        else {
            Node *p = cariNode(posisi - 1);
            target = p->next;
            p->next = target->next;
        }

        delete target;
        jumlah--;

        if (jumlah == 0) {
            head = tail = nullptr;
        }

        perbaikiLink();

        cout << "Data berhasil dihapus!\n";
    }

    // MENAMPILKAN DATA
    void tampilkan() {
        if (head == nullptr) {
            cout << "\nData mahasiswa masih kosong!\n";
            return;
        }

        Node *p = head;

        cout << "\n========== DATA MAHASISWA ==========\n";

        for (int i = 0; i < jumlah; i++) {
            cout << "\nData ke-" << i + 1;
            cout << "\nNIM          : " << p->data.nim;
            cout << "\nNama         : " << p->data.nama;
            cout << "\nNilai Tugas  : " << p->data.tugas;
            cout << "\nKehadiran    : " << p->data.kehadiran;
            cout << "\nNilai UTS    : " << p->data.uts;
            cout << "\nNilai UAS    : " << p->data.uas;

            cout << fixed << setprecision(2);
            cout << "\nMutu Angka   : " << p->data.mutuAngka;
            cout << "\nMutu Huruf   : " << p->data.mutuHuruf;

            cout << "\n------------------------------------";

            p = p->next;
        }
    }
};

// INPUT NILAI
double inputNilai(string keterangan) {
    double nilai;

    do {
        cout << keterangan;
        cin >> nilai;

        if (cin.fail()) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Masukkan angka yang benar!\n";
            nilai = -1;
        }
        else if (nilai < 0 || nilai > 100) {
            cout << "Nilai harus 0 sampai 100!\n";
        }

    } while (nilai < 0 || nilai > 100);

    return nilai;
}

// INPUT DATA MAHASISWA
Mahasiswa inputMahasiswa() {
    Mahasiswa m;

    cout << "\n===== INPUT DATA MAHASISWA =====\n";

    cout << "Masukkan NIM       : ";
    cin >> m.nim;

    cin.ignore(numeric_limits<streamsize>::max(), '\n');

    cout << "Masukkan Nama      : ";
    getline(cin, m.nama);

    m.tugas = inputNilai("Nilai Tugas        : ");
    m.kehadiran = inputNilai("Nilai Kehadiran    : ");
    m.uts = inputNilai("Nilai UTS          : ");
    m.uas = inputNilai("Nilai UAS          : ");

    // MENGHITUNG MUTU ANGKA
    m.mutuAngka =
        (m.tugas * 0.20) +
        (m.kehadiran * 0.15) +
        (m.uts * 0.30) +
        (m.uas * 0.35);

    // MENENTUKAN MUTU HURUF
    if (m.mutuAngka < 45)
        m.mutuHuruf = 'E';
    else if (m.mutuAngka < 55)
        m.mutuHuruf = 'D';
    else if (m.mutuAngka < 65)
        m.mutuHuruf = 'C';
    else if (m.mutuAngka < 75)
        m.mutuHuruf = 'B';
    else
        m.mutuHuruf = 'A';

    return m;
}

// MENU OPERASI LINKED LIST
void menuOperasi(LinkedList &list) {
    char pilih;

    do {
        cout << "\n===== MENU OPERASI LINKED LIST =====\n";
        cout << "a. Input Data Mahasiswa\n";
        cout << "b. Sisip Depan\n";
        cout << "c. Sisip Tengah\n";
        cout << "d. Sisip Belakang\n";
        cout << "e. Hapus Depan\n";
        cout << "f. Hapus Tengah\n";
        cout << "g. Hapus Belakang\n";
        cout << "h. Tampilkan Data Mahasiswa\n";
        cout << "i. Kembali ke Menu Utama\n";

        cout << "\nPilih Menu: ";
        cin >> pilih;

        switch (pilih) {

        case 'a': {
            Mahasiswa m = inputMahasiswa();
            list.sisip(list.ukuran() + 1, m);
            break;
        }

        case 'b': {
            Mahasiswa m = inputMahasiswa();
            list.sisip(1, m);
            break;
        }

        case 'c': {
            Mahasiswa m = inputMahasiswa();

            int posisi;
            cout << "Masukkan posisi: ";
            cin >> posisi;

            list.sisip(posisi, m);
            break;
        }

        case 'd': {
            Mahasiswa m = inputMahasiswa();
            list.sisip(list.ukuran() + 1, m);
            break;
        }

        case 'e':
            list.hapus(1);
            break;

        case 'f': {
            int posisi;
            cout << "Masukkan posisi data: ";
            cin >> posisi;

            list.hapus(posisi);
            break;
        }

        case 'g':
            list.hapus(list.ukuran());
            break;

        case 'h':
            list.tampilkan();
            break;

        case 'i':
            cout << "Kembali ke menu utama...\n";
            break;

        default:
            cout << "Pilihan tidak tersedia!\n";
        }

    } while (pilih != 'i');
}

// PROGRAM UTAMA
int main() {
    LinkedList single(1);
    LinkedList doubly(2);
    LinkedList circular(3);

    int pilihan;

    do {
        cout << "\n========== MENU UTAMA ==========\n";
        cout << "1. Single Linked List\n";
        cout << "2. Double Linked List\n";
        cout << "3. Circular Linked List\n";
        cout << "4. Keluar\n";

        cout << "\nPilih Menu: ";
        cin >> pilihan;

        switch (pilihan) {

        case 1:
            cout << "\n=== SINGLE LINKED LIST ===\n";
            menuOperasi(single);
            break;

        case 2:
            cout << "\n=== DOUBLE LINKED LIST ===\n";
            menuOperasi(doubly);
            break;

        case 3:
            cout << "\n=== CIRCULAR LINKED LIST ===\n";
            menuOperasi(circular);
            break;

        case 4:
            cout << "\nProgram selesai. Terima kasih!\n";
            break;

        default:
            cout << "\nPilihan tidak valid!\n";
        }

    } while (pilihan != 4);

    return 0;
}
