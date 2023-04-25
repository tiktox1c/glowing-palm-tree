#include <iostream>
#include <fstream>
#include <sstream>
#include <string>
using namespace std;

const int TABLE_SIZE = 10000;

class HashTable {
private:
    struct HashNode {
        string key;
        int count;
        HashNode* next;
    };

    HashNode* table[TABLE_SIZE];
    int collisionCount;


    // хеш-функция для вычисления индекса в таблице по ключу
    int hash(string key) {
        int sum = 0;
        for (int i = 0; i < key.length(); i++) {
            sum += (int)key[i];
        }
        return sum % TABLE_SIZE;
    }

    // метод для удаления знаков препинания из строки
    string removePunctuation(string str) {
        string result = "";
        for (int i = 0; i < str.length(); i++) {
            char c = str[i];
            if ((c >= 'a' && c <= 'z') || (c >= 'A' && c <= 'Z') || c == ' ') {
                result += c;
            }
        }
        return result;
    }


    int linearProbing(int index, int i) {
        return (index + i) % TABLE_SIZE;
    }

public:
    // конструктор по умолчанию
    HashTable() {
        for (int i = 0; i < TABLE_SIZE; i++) {
            table[i] = nullptr;
        }
        collisionCount = 0;
    }

    // деструктор для удаления хеш-таблицы
    ~HashTable() {
        for (int i = 0; i < TABLE_SIZE; i++) {
            HashNode* current = table[i];
            while (current != nullptr) {
                HashNode* temp = current;
                current = current->next;
                delete temp;
            }
            table[i] = nullptr;
        }
    }

    // метод для добавления элемента в таблицу
    void insert(string key) {
        int index = hash(key);
        int i = 0; // счетчик коллизий
        while (table[index] != nullptr) { // если в этой ячейке есть элемент
            if (table[index]->key == key) { // если ключ уже существует, увеличиваем счетчик
                table[index]->count++;
                return;
            }
            // иначе продолжаем искать следующую свободную ячейку
            index = linearProbing(index, ++i); // вычисляем новый индекс с помощью линейного пробирования
            collisionCount++; // увеличиваем счетчик коллизий при каждой коллизии
        }
        // создаем новый узел с ключом key, значением 1 и добавляем его в таблицу
        HashNode* newNode = new HashNode;
        newNode->key = key;
        newNode->count = 1;
        newNode->next = nullptr;
        table[index] = newNode;
    }

    int getCollisionCount() {
        return collisionCount;
    }

    // метод для вывода 10 слов, которые встречаются чаще всего в таблице
    void printMostFrequent() {
        string mostFrequent[10];
        int counts[10] = { 0 };
        for (int i = 0; i < TABLE_SIZE; i++) {
            HashNode* current = table[i];
            while (current != nullptr) {
                int minCountIndex = 0;
                int minCount = counts[0];
                // проверяем, является ли текущий элемент одним из 10 наиболее часто встречающихся слов
                for (int j = 0; j < 10; j++) {
                    if (counts[j] < minCount)
                    {
                        minCount = counts[j];
                        minCountIndex = j;
                    }

                    if (current->count > counts[j]) {
                        // текущий элемент встретился чаще, чем j-е слово из списка наиболее часто встречающихся слов
                        // заменяем j-е слово на текущее
                        counts[minCountIndex] = current->count;
                        mostFrequent[minCountIndex] = current->key;
                        break;
                    }
                    else if (current->count == counts[j] && current->key < mostFrequent[j]) {
                        // текущее слово встречается столько же раз, что и j-е слово из списка, но лексикографически оно меньше
                        // заменяем j-е слово на текущее
                        mostFrequent[j] = current->key;
                        break;
                    }
                }
                current = current->next;
            }
        }

        cout << "10 most frequent words:" << "\n\n";
        for (int i = 0; i < 10; i++) {
            cout << mostFrequent[i] << " (" << counts[i] << " times)" << '\n';
        }
    }

    // метод для считывания текста из файла и добавления его в таблицу
    void readTextFromFile(string fileName) {
        ifstream inputFile(fileName);
        if (inputFile.is_open()) {
            string line;
            while (getline(inputFile, line)) {
                line = removePunctuation(line);
                string word = "";
                for (int i = 0; i < line.length(); i++) {
                    char c = line[i];
                    if (c != ' ') {
                        word += c;
                    }
                    else if (word != "") {
                        insert(word);
                        word = "";
                    }
                }
                if (word != "") {
                    insert(word);
                }
            }
            inputFile.close();
        }
        else {
            cout << "Error: could not open file \"" << fileName << "\"" << endl;
        }
    }
};

