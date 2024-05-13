#include <iostream>
#include <iomanip>
#include <fstream>
#include <vector>
#include <stdexcept>
#include <ctime>
using namespace std;

//using namespace 
namespace cal{
int dayNumber(int day, int month, int year) {
   static int A[] = { 0, 3, 2, 5, 0, 3, 5, 1, 4, 6, 2, 4 };
   year -= month < 3;
   return ( year + year/4 - year/100 + year/400 + A[month-1] + day) % 7;
}

string getMonthName(int monthNumber) {
   string M[] = {"January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"};
   return (M[monthNumber]);
}

int numberOfDays(int monthNumber, int year) {
   switch(monthNumber) {
      case 0 :
      case 2 :
      case 4 :
      case 6 :
      case 7 :
      case 9 :
      case 11: return(31); // for months of 31 days
      break;
      case 1 :
         if (year % 400 == 0 || (year % 4 == 0 && year %100 != 0))
            return (29);
         else
            return (28);
      break;
      case 3 :
      case 5 :
      case 8 :
      case 10 : return(30); // for months of 30 days
      break;
   }
}

void printCalendarr(int month, int year) {
    cout << "   ------------------------------" << endl;
    cout << "          CALENDAR PPLICATION  " << month << "/" << year << endl;
    cout << "   ------------------------------" << endl;
    cout << "   Sun  Mon  Tue   Wed    Thu    Fri    Sat \n";

    int days = numberOfDays(month - 1, year);
    int current = dayNumber(1, month, year);

    int k;
    for (k = 0; k < current; k++)
        cout << setw(5) << " ";
    for (int j = 1; j <= days; j++) {
        printf("%5d", j);
        if (++k > 6) {
            k = 0;
            cout << endl;
        }
    }
    if (k)
        cout << endl;

    cout << "   ------------------------------" << endl;
}

}
using namespace cal;
class Calendar {
protected:
    int month;
    int year;

public:
    Calendar(int m, int y) : month(m), year(y) {}

    Calendar() {}

    virtual void printCalendar() = 0;

    virtual void printEvents() const = 0;

    virtual void saveToFile(const string& filename) const = 0;
};

class GregorianCalendar : public Calendar {
public:
    GregorianCalendar(int m, int y) : Calendar(m, y) {}

   void printCalendar() override {
    printCalendarr( month, year);
}


    void printEvents() const override {
        cout << "No events in the Gregorian calendar." << endl;
    }

    void saveToFile(const string& filename) const override {
        cout << "No events to save in the Gregorian calendar." << endl;
    }
};


class Date {
protected:
    int day;
    int month;
    int year;
public:
    Date(int d, int m, int y) : day(d), month(m), year(y) {}

    int getDay() const { return day; }
    int getMonth() const { return month; }
    int getYear() const { return year; }

    void print() const {
        cout << day << "/" << month << "/" << year;
    }
};

class Event {
protected:
    Date date;
    string title;
public:
    Event(const Date& d, const string& t) : date(d), title(t) {}

    const Date& getDate() const { return date; }
    const string& getTitle() const { return title; }

    void print() const {
        cout << "Date: ";
        date.print();
        cout << ", Title: " << title << endl;
    }
};

class UserCalendar : public Calendar {
private:
    string username;
    vector<Event*> events;
public:
    UserCalendar(const string& name) : username(name) {}

    void addEvent(Event* event) {
        events.push_back(event);
    }

    void printCalendar() override {
        cout << "No calendar to print for user: " << username << endl;
    }

    void printEvents() const override {
        if (events.empty()) {
            cout << "No events in " << username << "'s calendar." << endl;
            return;
        }
        cout << "Events in " << username << "'s calendar:" << endl;
        for (const auto& event : events) {
            event->print();
        }
    }

    void saveToFile(const string& filename) const override {
        ofstream file(filename);
        if (!file.is_open()) {
            throw runtime_error("Failed to open file for writing.");
        }

        for (const auto& event : events) {
            file << event->getDate().getDay() << "/"
                 << event->getDate().getMonth() << "/"
                 << event->getDate().getYear() << ","
                 << event->getTitle() << endl;
        }

        file.close();
    }
};

int main() {
    char choice;
    do {
        int option;
        cout<<endl<<endl;
        cout << "                                     Welcome to the Calendar Application" << endl<<endl<<endl;
        cout << "1. View Calendar" << endl;
        cout << "2. Manage Events" << endl;
        cout << "3. About Developers " <<endl;
        cout << "Enter your choice: ";
        cin >> option;

        switch (option) {
            case 1: {
                int month, year;
                cout << "Enter month (1-12): ";
                cin >> month;
                cout << "Enter year: ";
                cin >> year;

                Calendar *cal = new GregorianCalendar(month, year);
                cal->printCalendar();
                delete cal;
                break;
            }

            case 2: {
                  string name;
                    string note;
                    int d,m,y;
                    cout << "Enter note : " << endl;
                    cin>>note;
                    cout<<" Enter name : " <<endl;
                   cin>>name;
                   
                      cout << "Enter Day: " << endl;
                      cin>>d;
                     cout << "Enter Month : " << endl;
                     cin>>m;
                         cout << "Enter Year: " << endl;
                         cin>>y;
                         
                
                
                try {
                  
                    

                    UserCalendar userCalendar(name);

                    // Adding events
                    userCalendar.addEvent(new Event(Date(d, m, y), note));
                    cout<<" Event is successfully added :" <<endl;
                   
              //   cout<<"Show the Event ,,,write Y for show event visa verse "<<endl;
              cout<<endl<<endl;
                
                 
                    // Printing events
                    userCalendar.printEvents();
                 
                    // Saving events to file
                    userCalendar.saveToFile("events.txt");

                    cout << "Events saved to file 'events.txt'." << endl;
                } catch (const exception& ex) {
                    cerr << "Error: " << ex.what() << endl;
                }
                break;
            }
            case 3:
                    cout << "\t\t\t\t\t\t\t\t ====================================="<< endl;
        cout << " \t\t\t\t\t\t\t\t ===        ABOUT DEVELOPER       ===" << endl;
        cout << "\t\t\t\t\t\t\t\t|=====================================" << endl;
        cout << "\t\t\t\t\t\t\t\t|    DEVELOPER: Hamza  Ali             |" << endl;
        cout << "\t\t\t\t\t\t\t\t|                Khan Muhammad         |" << endl;
        cout << "\t\t\t\t\t\t\t\t|                Subhan                |" << endl;
        cout << "\t\t\t\t\t\t\t\t|               Hammad Abid            |" << endl;
        cout << "\t\t\t\t\t\t\t\t|   SEMESTER:  SECOND                  |" <<endl;
        cout << "\t\t\t\t\t\t\t\t|   ROLL NO:   218,028.277,214         |" <<endl;
        cout << "\t\t\t\t\t\t\t\t|   SUBMIT TO: Said Nabi               |" <<endl;
        cout << "\t\t\t\t\t\t\t\t|===================================== |" << endl;

            default:
                cout << "Invalid choice." << endl;
        }
        
        cout << "Do you want to continue (y/n)? ";
        cin >> choice;
    } while (tolower(choice) == 'y');

    return 0;
}
