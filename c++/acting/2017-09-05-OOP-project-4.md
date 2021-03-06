---
layout: post
title:  "Cpp로 만드는 OOP 단계 별 프로젝트 04단계"
date:   2017-09-05 15:20:00
categories: [Cpp]
tags: OOP_project
comments: true
---
<h2>Bank System Ver0.4</h2>  

<!--more-->

OOP(Object Oriented Programming, 객체 지향 프로그래밍)을 학습하면서 OOP의 주요 특징(캡슐화, 상속, 다형성)을 보다 확실하게 이해하기 위해 이 프로젝트를 진행한다. 프로젝트의 진행 과정은 총 11단계로 구성되어있다. 프로그램의 구성은 달라질 수 있으나 그 초기 단계는 BankingSystem(은행 시스템)으로 한다.

---

이번에도 Ver0.3에서처럼 간단하게 수정할 것이다. 물론 중요하지 않은 것은 아니니 이번 단계의 프로젝트를 진행하여 우리가 구현하고 있는 프로젝트의 완성도를 높여보도록 하자.  

지금까지 배운 const를 추가하여 코드의 안전성을 높이는 작업이 주가 될 것이고, Account 클래스의 멤버함수 중 일부를 const로 선언해보고, const로 선언할 수 있는 모든 멤버함수를 const로 선언하는 작업을 할 것이다.  

{% highlight Cpp %}

#include <iostream>
#include <cstring>
using namespace std;
const int NAME_LEN = 20;

void ShowMenu();		// 메뉴 출력
void MakeAccount();		// 개좌 개설
void DepositMoney();	// 입금
void WithdrawMoney();	// 출금
void ShowAllAccInfo();	// 잔액 조회

enum { MAKE = 1, DEPOSIT, WITHDRAW, INQUIRE, EXIT };

class Account
{
private:
	int accID;
	int balance;
	char* name;
public:
	Account(int accID, int balance, char* name)
		: accID(accID), balance(balance)
	{
		this->name = new char[strlen(name) + 1];
		strcpy_s(this->name, strlen(name) + 1, name);
	}
    Account(const Account &copy)
    	: accID(copy.accID), balance(copy.balance)
    {
		this->name = new char[strlen(copy.name) + 1];
		strcpy_s(this->name, strlen(name) + 1, copy.name);
    }
	int GetAccID() const { return accID; }

	void Deposit(int money)		// 입금
	{
		balance += money;
	}

	void Withdraw(int money)		// 출금
	{
		if (balance < money)
		{
			cout << "잔액 부족" << endl;
			return;
		}
		balance -= money;
		cout << "출금 완료" << endl;

	}

	void ShowAccInfo() const
	{
		cout << "계좌 ID: " << accID << endl;
		cout << "이 름: " << name << endl;
		cout << "잔 고: " << balance << endl;
	}
	~Account()
	{
		delete[]name;
	}
};

Account* accArr[100];
int accNum = 0;

int main()
{
	int choice;

	while (1)
	{
		ShowMenu();
		cout << "선택: ";
		cin >> choice;
		cout << endl;

		switch (choice)
		{
		case MAKE:
			MakeAccount();
			break;
		case DEPOSIT:
			DepositMoney();
			break;
		case WITHDRAW:
			WithdrawMoney();
			break;
		case INQUIRE:
			ShowAllAccInfo();
			break;
		case EXIT:
			for (int i = 0; i < accNum; i++)
				delete accArr[i];
			return 0;
			break;
		default:
			cout << "Illegal selection.." << endl;
		}
	}
}

void ShowMenu()
{
	cout << "-----MENU-----" << endl;
	cout << "1. 계좌 개설" << endl;
	cout << "2. 입 금" << endl;
	cout << "3. 출 금" << endl;
	cout << "4. 계좌정보 전체 출력" << endl;
	cout << "5. 프로그램 종료" << endl;
}

void MakeAccount()
{
	int id;
	char name[NAME_LEN];
	int balance;

	cout << "[개좌 개설]" << endl;
	cout << "계좌 ID: "; cin >> id;
	cout << "이름: "; cin >> name;
	cout << "입금액: "; cin >> balance;
	cout << endl;

	//accArr[accNum] = new Account(id, balance, name);
	accArr[accNum++] = new Account(id, balance, name);
}

void DepositMoney()
{
	int money;
	int id;

	cout << "[입 금]" << endl;
	cout << "계좌ID: "; cin >> id;
	cout << "입금액: "; cin >> money;

	for (int i = 0; i < accNum; i++)
	{
		if (accArr[i]->GetAccID() == id)
		{
			accArr[i]->Deposit(money);
			cout << "입금 완료" << endl << endl;
			return;
		}
	}
	cout << "유효하지 않은 ID입니다." << endl << endl;
}

void WithdrawMoney()
{
	int money;
	int id;
	cout << "[출 금]" << endl;
	cout << "계좌ID: "; cin >> id;
	cout << "출금액: "; cin >> money;

	for (int i = 0; i < accNum; i++)
		if (accArr[i]->GetAccID() == id)
			accArr[i]->Withdraw(money);
}

void ShowAllAccInfo()
{
	for (int i = 0; i < accNum; i++)
	{
		accArr[i]->ShowAccInfo();
		cout << endl;
	}
}

{% endhighlight %}  

><h5>어려웠던 점</h5>

> 간단하게 const가 필요한 멤버함수에 const 키워드를 추가하는 작업이 진행되었다. 사실 논리적으로 '어디에 const를 넣어야하지?'라고 잠깐 고민했지만, 값의 변화가 없는 부분에만 집어넣어 코드의 안전성을 높였다.  

