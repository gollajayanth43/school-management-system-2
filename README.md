#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define FILE_NAME "students.txt"

typedef struct {
    int roll;
    char name[50];
    int age;
    char grade[5];
} Student;

void addStudent();
void displayStudents();
void searchStudent();
void modifyStudent();
void deleteStudent();

int main() {
    int choice;

    while (1) {
        printf("\n==== SCHOOL MANAGEMENT SYSTEM ====\n");
        printf("1. Add Student\n");
        printf("2. Display All Students\n");
        printf("3. Search Student\n");
        printf("4. Modify Student\n");
        printf("5. Delete Student\n");
        printf("6. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: addStudent(); break;
            case 2: displayStudents(); break;
            case 3: searchStudent(); break;
            case 4: modifyStudent(); break;
            case 5: deleteStudent(); break;
            case 6: printf("Exiting...\n"); exit(0);
            default: printf("Invalid choice! Please try again.\n");
        }
    }
    return 0;
}

void addStudent() {
    FILE *fp = fopen(FILE_NAME, "a");
    if (!fp) {
        printf("Error opening file!\n");
        return;
    }

    Student s;
    printf("\nEnter Roll Number: ");
    scanf("%d", &s.roll);
    printf("Enter Name: ");
    getchar(); // clear buffer
    fgets(s.name, sizeof(s.name), stdin);
    s.name[strcspn(s.name, "\n")] = 0;
    printf("Enter Age: ");
    scanf("%d", &s.age);
    printf("Enter Grade: ");
    scanf("%s", s.grade);

    fprintf(fp, "%d,%s,%d,%s\n", s.roll, s.name, s.age, s.grade);
    fclose(fp);
    printf("Student added successfully!\n");
}

void displayStudents() {
    FILE *fp = fopen(FILE_NAME, "r");
    if (!fp) {
        printf("No records found!\n");
        return;
    }

    Student s;
    printf("\n%-10s %-20s %-10s %-10s\n", "Roll", "Name", "Age", "Grade");
    printf("---------------------------------------------------\n");

    while (fscanf(fp, "%d,%49[^,],%d,%4s\n", &s.roll, s.name, &s.age, s.grade) == 4) {
        printf("%-10d %-20s %-10d %-10s\n", s.roll, s.name, s.age, s.grade);
    }

    fclose(fp);
}

void searchStudent() {
    FILE *fp = fopen(FILE_NAME, "r");
    if (!fp) {
        printf("No records found!\n");
        return;
    }

    int roll, found = 0;
    Student s;
    printf("Enter Roll Number to Search: ");
    scanf("%d", &roll);

    while (fscanf(fp, "%d,%49[^,],%d,%4s\n", &s.roll, s.name, &s.age, s.grade) == 4) {
        if (s.roll == roll) {
            printf("\nRecord Found:\n");
            printf("Roll: %d\nName: %s\nAge: %d\nGrade: %s\n", s.roll, s.name, s.age, s.grade);
            found = 1;
            break;
        }
    }

    if (!found)
        printf("Student with Roll Number %d not found!\n", roll);

    fclose(fp);
}

void modifyStudent() {
    FILE *fp = fopen(FILE_NAME, "r");
    FILE *temp = fopen("temp.txt", "w");
    if (!fp || !temp) {
        printf("Error opening file!\n");
        return;
    }

    int roll, found = 0;
    Student s;
    printf("Enter Roll Number to Modify: ");
    scanf("%d", &roll);

    while (fscanf(fp, "%d,%49[^,],%d,%4s\n", &s.roll, s.name, &s.age, s.grade) == 4) {
        if (s.roll == roll) {
            printf("Enter New Name: ");
            getchar();
            fgets(s.name, sizeof(s.name), stdin);
            s.name[strcspn(s.name, "\n")] = 0;
            printf("Enter New Age: ");
            scanf("%d", &s.age);
            printf("Enter New Grade: ");
            scanf("%s", s.grade);
            found = 1;
        }
        fprintf(temp, "%d,%s,%d,%s\n", s.roll, s.name, s.age, s.grade);
    }

    fclose(fp);
    fclose(temp);

    remove(FILE_NAME);
    rename("temp.txt", FILE_NAME);

    if (found)
        printf("Record updated successfully!\n");
    else
        printf("Student with Roll Number %d not found!\n", roll);
}

void deleteStudent() {
    FILE *fp = fopen(FILE_NAME, "r");
    FILE *temp = fopen("temp.txt", "w");
    if (!fp || !temp) {
        printf("Error opening file!\n");
        return;
    }

    int roll, found = 0;
    Student s;
    printf("Enter Roll Number to Delete: ");
    scanf("%d", &roll);

    while (fscanf(fp, "%d,%49[^,],%d,%4s\n", &s.roll, s.name, &s.age, s.grade) == 4) {
        if (s.roll != roll)
            fprintf(temp, "%d,%s,%d,%s\n", s.roll, s.name, s.age, s.grade);
        else
            found = 1;
    }

    fclose(fp);
    fclose(temp);

    remove(FILE_NAME);
    rename("temp.txt", FILE_NAME);

    if (found)
        printf("Record deleted successfully!\n");
    else
        printf("Student with Roll Number %d not found!\n", roll);
}
