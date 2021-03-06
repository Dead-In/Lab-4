МИНИСТЕРСТВО НАУКИ  И ВЫСШЕГО ОБРАЗОВАНИЯ РОССИЙСКОЙ ФЕДЕРАЦИИ  
Федеральное государственное автономное образовательное учреждение высшего образования  
"КРЫМСКИЙ ФЕДЕРАЛЬНЫЙ УНИВЕРСИТЕТ им. В. И. ВЕРНАДСКОГО"  
ФИЗИКО-ТЕХНИЧЕСКИЙ ИНСТИТУТ  
Кафедра компьютерной инженерии и моделирования
<br/><br/>
 
### Отчёт по лабораторной работе № 4<br/> по дисциплине "Программирование"
<br/>
 
студент 1 курса группы ПИ-б-о-192(2)  
Якубов Эмиль Эюпович 
направления подготовки 09.03.04 "Программная инженерия"  
<br/>
 
<table>
<tr><td>Научный руководитель<br/> старший преподаватель кафедры<br/> компьютерной инженерии и моделирования</td>
<td>(оценка)</td>
<td>Чабанов В.В.</td>
</tr>
</table>
<br/><br/>
 
Симферополь, 2020

<br><br><br>
### Цель: <br>
* Закрепить навыки работы с перечислениями;
* Закрепить навыки работы с структурами;
* Освоить методы составления многофайловых программ.


### Реализация: <br>
#### main.cpp <br>
```C++
#include <iostream>
#include "game_func.h"
using namespace std;
int main()
{
  setlocale(LC_ALL, "ru"); int sum = 0;
    char pChar;
    do {
        cout << "Выберите Х или 0: ";
        cin >> pChar;
    } while (pChar != 'X' && pChar != '0');
    Game mainGame = initGame(pChar);
    while (!updateGame(&mainGame))
    {
        if (mainGame.isUserTurn)userTurn(&mainGame);
        else botTurn(&mainGame);
        updateDisplay(mainGame);
    }
    if (mainGame.status == USER_WIN)cout << "Вы победили!";
    else if (mainGame.status == BOT_WIN)cout << "Вы проиграли!";
    else cout << "Ничья!";
}
```
</br><br>
#### game_func.h
```C++
#pragma once

  enum Status {
    PLAY,
    USER_WIN,
    BOT_WIN,
    NOT_WIN
  };
  struct Game
  {
    char bord[3][3];
    bool isUserTurn;
    char userChar;
    char botChar;
    Status status;
  };
  Game initGame(char userChar);
  void updateDisplay(const Game game);
  void botTurn(Game* game);
  void userTurn(Game* game);
  bool updateGame(Game* game);
``` 

#### game_func.cpp
```C++
#include "game_func.h"
#include <iostream>
#include <time.h>
using namespace std;

Game initGame(char userChar)
{
  srand(time(NULL));
  Game new_game;
  for (int i = 0; i < 3; i++)
    for (int j = 0; j < 3; j++)
      new_game.bord[i][j] = ' ';
  new_game.isUserTurn = bool(rand() % 2);
  new_game.userChar = userChar;
  new_game.status = PLAY;
  switch (new_game.userChar)
  {
  case '0': new_game.botChar = 'X'; break;
  case 'X': new_game.botChar = '0'; break;
  }
  return new_game;

}
void updateDisplay(const Game game)
{
  system("cls");

  cout << "a  " << "b  " << "c " << endl;
  cout << "__________" << endl;
  for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
      cout << "| " << game.bord[i][j] << "";
    }
    cout << "[ " << i << endl << "__________" << endl;
  }
}
void botTurn(Game* game)
{
  /*
  *
  Выполняет ход бота. В выбранную ячейку устанавливается символ которым играет бот.
  Бот должен определять строку, столбец или диагональ в которой у игрока больше всего иксиков/ноликов и ставить туда свой символ.
  Если на поле ещё нет меток, бот должен ставить свой знак в центр.
  В остальных случаях бот ходит рандомно.
  */
  bool isEmpty = true;
  for (int i = 0; i < 3; i++)
    for (int j = 0; j < 3; j++)
    {
      if (game->bord[i][j] != ' ')
      {
        isEmpty = 0; break;
      }
    } // проверка пустое ли поле
  if (isEmpty) game->bord[1][1] = game->botChar;
  else
  {

    for (int i = 0; i < 3; i++) // строка
    {
      int str = 0, col = 0;
      for (int j = 0; j < 3; j++)
      {
        if (game->bord[i][j] == game->userChar) str++;
        else if (game->bord[i][j] != game->userChar) col = j;
      }
      if (str == 2) {
        game->bord[i][col] = game->botChar; return;
      }
    }
    for (int i = 0; i < 3; i++) // столбец
    {
      int str = 0, col = 0;
      for (int j = 0; j < 3; j++)
      {
        if (game->bord[j][i] == game->userChar) str++;
        else if (game->bord[j][i] != game->userChar) col = j;
      }
      if (str == 2) {
        game->bord[col][i] = game->botChar; return;
      }
    }
    for (int i = 0; i < 3; i++)//главная диагональ массива
    {
      int str = 0, col = 0;
      for (int j = 0; j < 3; j++)
      {
        if (game->bord[i][i] == game->userChar) str++;
        else if (game->bord[i][i] != game->userChar) col = i;
      }
      if (str == 2) { game->bord[col][col] = game->botChar; return; }
    }
    for (int i = 0; i < 3; i++) //побочная диагональ массива
    {
      int str = 0, col = 0;
      for (int j = 0; j < 3; j++)
      {
        if (game->bord[i][2 - i] == game->userChar) str++;
        else if (game->bord[i][2 - i] != game->userChar) col = i;
      }
      if (str == 2) {
        game->bord[col][2 - col] = game->botChar; return;
      }
    }
    int str = 0, col = 0;
    while (game->bord[str][col] != ' ')
    {
      str = rand() % 3;
      col = rand() % 3;
    }
    game->bord[str][col] = game->botChar;
  }
}
void userTurn(Game* game)
{
  int x_coord = 0, y_coord = 0;
  bool is_x = true, is_y = true, two_bools = true;
  while (two_bools)
  {
    while (is_x)
    {
      cout << "введите х" << endl;
      cin >> x_coord;
      if (x_coord < 0 || x_coord > 2) cout << "Данные некорректны, повторите попытку" << endl;
      else is_x = false;
    }
    while (is_y)
    {
      cout << "введите y" << endl;
      cin >> y_coord;
      if (y_coord < 0 || y_coord > 2) cout << "Данные некорректны, повторите попытку!" << endl;
      else is_y = false;
    }
    if (game->bord[x_coord][y_coord] == ' ')two_bools = false;
    else
    {
      cout << "Ошибка, ошибок нет:))" << endl;
      is_x = true;
      is_y = true;
    }

  }
  game->bord[x_coord][y_coord] = game->userChar;
}
bool updateGame(Game* game)
{
  int counter;
  for (int i = 0; i < 3; i++)
  {
    counter = 0;
    for (int j = 0; j < 3; j++)
    {
      if (game->bord[i][j] == game->userChar) counter++;
      else if (game->bord[i][j] == game->botChar) counter--;
    }
    if (counter == 3) game->status = USER_WIN;
    else if (counter == -3)game->status = BOT_WIN;
  }
  for (int i = 0; i < 3; i++)
  {
    counter = 0;
    for (int j = 0; j < 3; j++)
    {
      if (game->bord[j][i] == game->userChar) counter++;
      else if (game->bord[j][i] == game->botChar) counter--;
    }
    if (counter == 3) game->status = USER_WIN;
    else if (counter == -3)game->status = BOT_WIN;
  }
  for (int i = 0; i < 3; i++)
  {
    counter = 0;
    if (game->bord[i][i] == game->userChar) counter++;
    else if (game->bord[i][i] == game->botChar) counter--;
    if (counter == 3) game->status = USER_WIN;
    else if (counter == -3)game->status = BOT_WIN;
  }
  for (int i = 0; i < 3; i++)
  {
    counter = 0;
    for (int j = 0; j < 3; j++)
    {

    }
  }
  if (game->status == PLAY) {
    counter = 0;
    for (int i = 0; i < 3; i++) {
      for (int k = 0; k < 3; k++) {
        if (game->bord[i][k] == ' ')counter++;
      }
    }
    if (!counter) {
      game->status = NOT_WIN;
      return true;
    }
    game->isUserTurn = !game->isUserTurn;
    return false;
  }
  return true;
}
```
**Вывод:** Были закреплены навыки работы с енумераторами, структурами, освоены методы составления многофайловых программ.
