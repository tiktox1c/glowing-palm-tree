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
