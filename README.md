#include "stdafx.h"
#include <iostream>
#include <sstream>

struct Complex {
    Complex(); //конструктор (без которого не получилось бы создавать контейнеры с этим классом) без параметров, который инициализирует поля структуры Complex{}
    explicit Complex(const double real); // 
    Complex(const double real, const double imaginary);  // тоже конструктор, но уже с параметрами
    bool operator==(const Complex& rhs) const // значение по умолчанию
    {
        return (re == rhs.re) && (im == rhs.im);
    }
    bool operator!=(const Complex& rhs) const // получение комлексного числа из действительного (i=0)
    {
        return !operator==(rhs); 
    }
    Complex& operator+=(const Complex& rhs); // получение копмплексного чилса из двух действительных путём +, -, *
    Complex& operator+=(const double rhs)
    {
        return operator+=(Complex(rhs));
    }
    Complex& operator-=(const Complex& rhs); // функция возвращает ссылку на Complex
    Complex& operator-=(const double rhs)
    {
        return operator-=(Complex(rhs));
    }
    Complex& operator*=(const Complex& rhs); // не очень понятно как работает переопределение операторов
    Complex& operator*=(const double rhs)
    {
        return operator*=(Complex(rhs));
    }
    std::ostream& wtiteTo(std::ostream& ostrm) const; 
    std::istream& readFrom(std::istream& istrm);
    double re{ 0.0 };
    double im{ 0.0 };

    static const char leftBrace{'{'};
    static const char separator{ ',' };
    static const char rightBrace{ '}' };
};
Complex operator+(const Complex& lhs, const Complex& rhs);  
Complex operator-(const Complex& lhs, const Complex& rhs); 
Complex operator*(const Complex& lhs, const Complex& rhs);
inline std::ostream& operator << (std::ostream& ostrm, const Complex& rhs)  
{
    return rhs.writeTo(ostrm);
}
inline std::istream& operator >> (std::istream& istrm, Complex& rhs)
{
    return rhs.readFrom(istrm);
}
bool testParse(const std::string& str)
{
    using namespace std; //зачем каждый раз вводить using namespace std;?
    istringstream istrm(str); // istringstream(str) - преобразование str в число
    Complex z; 
    istrm >> z;
    if (istrm.good())
    {
        cout << "Read success: " << str << " -> " << z << endl;
    }
    else {
        cout << "Read error: " << str << " -> " << z << endl;
    }
    return istrm.good();
}
int main()
{
    using namespace std; //зачем определять каждый раз
    Complex z; 
    z += Complex(8.0);
    testParse("{8.9, 9}");
    testParse("{8.9,  9}");
    testParse("{8.9, 9");
}
Complex::Complex(const double real)
    :Complex(real, 0.0)
{
}
Complex::Complex(const double real, const double imaginary)
    : re(real)
    , im(imaginary)
{}
Complex& Complex::operator+=(const Complex& rhs) //почему Complex повторяется два раза, причёб без наследования? 
{
    re += rhs.re;
    im += rhs.im;
    return *this;
}
Complex operator+(const Complex& lhs, const Complex& rhs)
{
    Complex sum(lhs);
    sum += rhs;
    return sum;
}
Complex& Complex:: operator-=(const Complex& rhs)
{
    re -= rhs.re;
    im -= rhs.im;
}
Complex operator-(const Complex& lhs, const Complex& rhs)
{
    return Complex(lhs.re - rhs.re, lhs.im - rhs.im);
}
ist
std::ostream& Complex::writeTo(std::ostream& ostrm) const
{
    ostrm << leftBrace << re << separator << im << rightBrace;
    return ostrm;
}
std::istream& Complex::readFrom(std::istream& istrm)
{
    char leftBrace(0);
    double real(0.0);
    double imaginary(0);
    char rightBrace(0);
    istrm >> leftBrace >> real >> separator >> imaginary >> rightBrace;
    if (istrm.good())
    {
        if ((Complex::leftBrace == leftBrace) && (Complex::rightBrace == rightBrace))
        {
            re = real;
            im = imaginary;
        }
        else
        {
            istrm.setstate(std::ios_base::failbit);
        }
    }
    return istrm;
}
