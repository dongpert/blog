---
title: lock Statement (C# Reference)
tags:
---
The lock keyword marks a statement block as a critical section by obtaining the mutual-exclusion lock for a given object, executing a statement, and then releasing the lock. The following example includes a lock statement.

lcok 키워드는 주어진 객체에 대해 상호 베타적 잠금을 획득하여 구문을 실행하고 잠금을 해제하여 구문 블록을 임계 영역으로 표시합니다.

~~~
class Account  
{  
    decimal balance;  
    private Object thisLock = new Object();  

    public void Withdraw(decimal amount)  
    {  
        lock (thisLock)  
        {  
            if (amount > balance)  
            {  
                throw new Exception("Insufficient funds");  
            }  
            balance -= amount;  
        }  
    }  
}  
~~~

### Remarks
The lock keyword ensures that one thread does not enter a critical section of code while another thread is in the critical section. If another thread tries to enter a locked code, it will wait, block, until the object is released.

잠금 키워드는 하나의 쓰레드가 다른 쓰레드가 임계 영역에 있는 동안에 코드의 임계 영역으로 들어가지 못하도록 합니다. 만약 다른 쓰레드가 잠긴 코드에 들어가려고 하면 객체가 해제될 때까지 블록을 기다릴 것입니다.

The section Threading discusses threading.

The lock keyword calls Enter at the start of the block and Exit at the end of the block. A ThreadInterruptedException is thrown if Interrupt interrupts a thread that is waiting to enter a lock statement.

잠금 키워드는 블록의 시작에서 Enter와 블록의 끝에서 Exit를 호출합니다. Interrupt가 잠금 구문에 들어가기를 기다리는 쓰레드를 중단하면 ThreadInterruptedException이 던져집니다.

In general, avoid locking on a public type, or instances beyond your code's control. The common constructs lock (this), lock (typeof (MyType)), and lock ("myLock") violate this guideline:

일반적으로 공용 형식이나 코드의 제어를 넘어서는 인스턴스를 잠금을 피하십시오. 일반적인 구조체 lock(this), lock(typeof(MyType)) 그리고 lock("MyLock")은 지침을 위반합니다.

lock (this) is a problem if the instance can be accessed publicly.

인스턴스에 공개적으로 접근할 수 있는 경우 lock(this)는 문제입니다.

lock (typeof (MyType)) is a problem if MyType is publicly accessible.

lock("myLock") is a problem because any other code in the process using the same string, will share the same lock.

Best practice is to define a private object to lock on, or a private static object variable to protect data common to all instances.

모범 사례는 잠글 private 객체를 정의하거나 모든 인스턴스에 공통된 데이터를 보호하기 위해 private static 변수를 것입니다.

You can't use the await keyword in the body of a lock statement.

### Example
The following sample shows a simple use of threads without locking in C#.
~~~
//using System.Threading;

class ThreadTest
{
    public void RunMe()
    {
        Console.WriteLine("RunMe called");
    }

    static void Main()
    {
        ThreadTest b = new ThreadTest();
        Thread t = new Thread(b.RunMe);
        t.Start();
    }
}
// Output: RunMe called
~~~

### Example
The following sample uses threads and lock. As long as the lock statement is present, the statement block is a critical section and balance will never become a negative number.

다음 에제는 쓰레드와 잠금을 사용합니다. 잠금 구문이 존재하는 한 구문 블륵은 임계영역이고 절대 음수가 되지 않습니다.
~~~
class Account
{
    private Object thisLock = new Object();
    int balance;

    Random r = new Random();

    public Account(int initial)
    {
        balance = initial;
    }

    int Withdraw(int amount)
    {

        // This condition never is true unless the lock statement
        // is commented out.
        if (balance < 0)
        {
            throw new Exception("Negative Balance");
        }

        // Comment out the next line to see the effect of leaving out 
        // the lock keyword.
        lock (thisLock)
        {
            if (balance >= amount)
            {
                Console.WriteLine("Balance before Withdrawal :  " + balance);
                Console.WriteLine("Amount to Withdraw        : -" + amount);
                balance = balance - amount;
                Console.WriteLine("Balance after Withdrawal  :  " + balance);
                return amount;
            }
            else
            {
                return 0; // transaction rejected
            }
        }
    }

    public void DoTransactions()
    {
        for (int i = 0; i < 100; i++)
        {
            Withdraw(r.Next(1, 100));
        }
    }
}

class Test
{
    static void Main()
    {
        Thread[] threads = new Thread[10];
        Account acc = new Account(1000);
        for (int i = 0; i < 10; i++)
        {
            Thread t = new Thread(new ThreadStart(acc.DoTransactions));
            threads[i] = t;
        }
        for (int i = 0; i < 10; i++)
        {
            threads[i].Start();
        }
        
        //block main thread until all other threads have ran to completion.
        foreach (var t in threads)
            t.Join();
    }
}
~~~