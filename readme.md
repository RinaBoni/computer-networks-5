
#include <iostream>
#include <string>
#include <vector>
#include <sstream>
#include <stdio.h>
#include <math.h>
using namespace std;



int getOctetsIP(string ip, vector<int>& octetsIP) {		// Define vector<int> octets, using reference from main
	stringstream sip(ip);								// use stringstream named ss and populate with ip
	string temp;
	octetsIP.clear();									// ощишаем вектор(для того что бы могли запустить функцию еще раз
	vector<bool> ipInRange;
	while (getline(sip, temp, '.'))						// Every time getline recieves new stream element from ss, save to temp
		octetsIP.push_back(atoi(temp.c_str()));			//... until reaches '.' delimiter, then push_back octet with new element.
	if (octetsIP.size() == 4) {
		for (int i = 0; i < octetsIP.size(); i++) {
			if (octetsIP[i] >= 0 && octetsIP[i] <= 255)
				ipInRange.push_back(true);
			else
				ipInRange.push_back(false);
		}
		if (ipInRange[0] == true && ipInRange[1] == true && ipInRange[2] == true && ipInRange[3] == true) {
			return 0;
		}
		else {
			cout << endl << "В октете всего 255 бит. Введите IP адрес заново." << endl << endl;
			return 1;
		}
	}
	else {
		cout << endl << "Введите 4 октеты разделяя их точками" << endl << endl;
		return 1;
	}
}




int getOctetsMask(string mask, vector<int>& octetsMask) {
	stringstream smask(mask);
	string temp;
	octetsMask.clear();		// Clears the octetsMask vector, in case main function re-runs this function
	vector<bool> maskInRange;
	while (getline(smask, temp, '.'))
		octetsMask.push_back(atoi(temp.c_str()));
	if (octetsMask.size() == 4) {
		for (int i = 0; i < octetsMask.size(); i++) {
			if (octetsMask[i] == 0 || octetsMask[i] == 128 || octetsMask[i] == 192 || octetsMask[i] == 224 || octetsMask[i] == 240 || octetsMask[i] == 248 || octetsMask[i] == 252 || octetsMask[i] == 254 || octetsMask[i] == 255)
				maskInRange.push_back(true);
			else
				maskInRange.push_back(false);
		}
		if (maskInRange[0] == true && maskInRange[1] == true && maskInRange[2] == true && maskInRange[3] == true) {
			return 0;
		}
		else {
			cout << endl << "Маска подсети использует только 2^[0-7]. Введите заново." << endl << endl;
			return 1;
		}
	}
	else {
		cout << endl << "Введите 4 октеты разделяя их точками." << endl << endl;
		return 1;
	}
}




int calcClass(vector<int>& octetsIP) {
	if (octetsIP[0] == 10) {
		return 1;	// Class A Private address blocks //
	}
	else if (octetsIP[0] == 172 && octetsIP[1] >= 16 && octetsIP[1] <= 31) {
		return 2;	// Class B Private address blocks //
	}
	else if (octetsIP[0] == 192 && octetsIP[1] == 168) {
		return 3;	// Class C Private address blocks //
	}
	else if (octetsIP[0] == 127) {
		return 4;	// Loopback Address Reserved address blocks //
	}
	else if (octetsIP[0] >= 0 && octetsIP[0] < 127) {
		return 5;
	}
	else if (octetsIP[0] > 127 && octetsIP[0] < 192) {
		return 6;
	}
	else if (octetsIP[0] > 191 && octetsIP[0] < 224) {
		return 7;
	}
	else if (octetsIP[0] > 223 && octetsIP[0] < 240) {
		return 8;
	}
	else if (octetsIP[0] > 239 && octetsIP[0] <= 255) {
		return 9;
	}
	else {
		return 0;	// Out of Range //
	}
}




//Бинарное представление /--
int getNHBits(vector<int>& octetsIP, vector<int>& octetsMask, vector<int>& octetsIPBits, vector<int>& octetsMaskBits) {

	// Бинарное представление ip адресв. // 
	cout << "------------------------------------------" << endl;
	cout << "///////// Бинарное представление //////////" << endl;
	cout << "------------------------------------------" << endl;
	for (int j = 0; j < octetsIP.size(); j++)
	{
		if (j > 0)
			cout << ".";

		int mask = 128;
		while (mask)
		{
			octetsIPBits.push_back((octetsIP[j] & mask) != 0);
			cout << ((octetsIP[j] & mask) != 0);
			mask >>= 1;
		}
	}
	cout << "  : IP адресс" << endl;

	// Бинарное представление маски. // 
	for (int j = 0; j < octetsMask.size(); j++)
	{
		if (j > 0)
			cout << ".";
		int mask = 128;
		while (mask)
		{
			octetsMaskBits.push_back((octetsMask[j] & mask) != 0);
			cout << ((octetsMask[j] & mask) != 0);
			mask >>= 1;
		}
	}
	cout << "  : Маска подсети" << endl;
	cout << "-----------------------------------------" << endl;

	return 0;
}



// Perform ANDing of IP and Subnet Mask to generate Network ID range //
vector<int> getNetID(vector<int>& octetsIPBits, vector<int>& octetsMaskBits) {
	vector<int> netID;
	for (int j = 0; j < octetsIPBits.size(); j++)
	{
		if ((j > 0) && (j % 8 == 0))
			cout << ".";

		netID.push_back(octetsIPBits[j] & octetsMaskBits[j]);
	}
	return netID;
}


// в строку
string toString(vector<int> octets) {
	ostringstream octStrm;

	for (int j = 0; j < octets.size(); j++)
	{
		if (j > 0)
			octStrm << '.';

		octStrm << octets[j];
	}

	return octStrm.str();
}


// перевод десятичного в двоичное
vector<int> toDecimal(vector<int> octets, vector<int>& decimals) {
	stringstream octStrm;
	decimals.clear();
	for (int j = 0; j < octets.size(); j++)
	{
		if (j > 0)
			octStrm << '.';

		octStrm << octets[j];
	}

	string temp;
	while (getline(octStrm, temp, '.'))
		decimals.push_back(atoi(temp.c_str()));

	return decimals;
}


// увеличение сети //
int getIncrement(vector<int> decimalMask, vector<int> decimalNetID) {
	int increment = 0;
	for (int i = 0; i < decimalMask.size(); i++) {
		if (decimalMask[i] == 255) {
			increment = 1;
		}
		else if (decimalMask[i] == 254) {
			increment = 2;
			break;
		}
		else if (decimalMask[i] == 252) {
			increment = 4;
			break;
		}
		else if (decimalMask[i] == 248) {
			increment = 8;
			break;
		}
		else if (decimalMask[i] == 240) {
			increment = 16;
			break;
		}
		else if (decimalMask[i] == 224) {
			increment = 32;
			break;
		}
		else if (decimalMask[i] == 192) {
			increment = 64;
			break;
		}
		else if (decimalMask[i] == 128) {
			increment = 128;
			break;
		}
	}
	return increment;
}

// Адрес сети
vector<int> getNetIDRange(vector<int>& decimalNetID, int& netInc, vector<int>& decimalMask) {
	vector<int> netIDEnd;
	for (int i = 0; i < decimalNetID.size(); i++) {
		if (decimalMask[i] == 255) {
			netIDEnd.push_back(decimalNetID[i]);
		}
		else if (decimalMask[i] < 255 && decimalMask[i] > 0) {
			netIDEnd.push_back((decimalNetID[i] + netInc) - 1);
		}
		else {
			netIDEnd.push_back(255);
		}
	}
	return netIDEnd;
}


//по количеству хостов
// количество подсетей
int getSubnets(vector<int>& decimalMask, int& ipClass, vector<int>& subClassMask) {
	int netBits = 0;
	subClassMask.clear();
	if (ipClass == 1) {
		subClassMask.push_back(255);
		subClassMask.push_back(0);
		subClassMask.push_back(0);
		subClassMask.push_back(0);
	}
	else if (ipClass == 2) {
		subClassMask.push_back(255);
		subClassMask.push_back(255);
		subClassMask.push_back(0);
		subClassMask.push_back(0);
	}
	else if (ipClass == 3) {
		subClassMask.push_back(255);
		subClassMask.push_back(255);
		subClassMask.push_back(255);
		subClassMask.push_back(0);
	}
	else if (ipClass == 4 || ipClass == 5) {
		subClassMask.push_back(decimalMask[0]);
		subClassMask.push_back(decimalMask[1]);
		subClassMask.push_back(decimalMask[2]);
		subClassMask.push_back(decimalMask[3]);
	}

	for (int i = 0; i < decimalMask.size(); i++) {
		if (decimalMask[i] != subClassMask[i]) {
			if (decimalMask[i] == 255) {
				netBits += 8;
				continue;
			}
			else if (decimalMask[i] == 254) {
				netBits += 7;
				continue;
			}
			else if (decimalMask[i] == 252) {
				netBits += 6;
				continue;
			}
			else if (decimalMask[i] == 248) {
				netBits += 5;
				continue;
			}
			else if (decimalMask[i] == 240) {
				netBits += 4;
				continue;
			}
			else if (decimalMask[i] == 224) {
				netBits += 3;
				continue;
			}
			else if (decimalMask[i] == 192) {
				netBits += 2;
				continue;
			}
			else if (decimalMask[i] == 128) {
				netBits += 1;
				continue;
			}
			else if (decimalMask[i] == 0) {
				netBits += 0;
				continue;
			}
			else {
				netBits += 0;
			}
		}
	}
	int subnets = pow(2.0, netBits);
	return subnets;
}



// количество хостов в подсети
int getHostsPerSubnet(vector<int>& decimalMask) {
	int hostBits = 0;
	for (int i = 0; i < decimalMask.size(); i++) {
		if (decimalMask[i] == 255) {
			hostBits += 0;
			continue;
		}
		else if (decimalMask[i] == 254) {
			hostBits += 1;
			continue;
		}
		else if (decimalMask[i] == 252) {
			hostBits += 2;
			continue;
		}
		else if (decimalMask[i] == 248) {
			hostBits += 3;
			continue;
		}
		else if (decimalMask[i] == 240) {
			hostBits += 4;
			continue;
		}
		else if (decimalMask[i] == 224) {
			hostBits += 5;
			continue;
		}
		else if (decimalMask[i] == 192) {
			hostBits += 6;
			continue;
		}
		else if (decimalMask[i] == 128) {
			hostBits += 7;
			continue;
		}
		else if (decimalMask[i] == 0) {
			hostBits += 8;
			continue;
		}
		else {
			hostBits = 0;
			break;
		}
	}
	int hostsPerSubnet = pow(2.0, hostBits) - 2;
	return hostsPerSubnet;
}


int main() {

	system("chcp 1251");

	
	char resp = 'y';
	while (resp == 'y') {
		
		cout << endl << endl;

		//ввод сети//
		string ip;
		vector<int> octetsIP;
		while (getOctetsIP(ip, octetsIP) == 1) {
			cout << "Введите адрес IPv4-> ";
			(getline(cin, ip));		// получаем ip адрес
		}

		//ввод маски//
		string mask;
		vector<int> octetsMask;
		while (getOctetsMask(mask, octetsMask) == 1) {
			cout << "Введите маску для подсети " << ip << " -> ";
			(getline(cin, mask));	// получаем маску подсети
		}
		cout << endl << endl << endl << endl << endl;

		//вывод ip Адреса и маски подсети//
		vector<int> decimals;
		cout << "//////////////////////////////////////////" << endl;
		cout << "/// IP Адрес: " << toString(octetsIP) << endl;
		vector<int> decimalMask = toDecimal(octetsMask, decimals);
		cout << "/// Маска подсети: " << toString(octetsMask) << endl;
		cout << "//////////////////////////////////////////" << endl << endl;

		// Распечатки бинарного представления//
		vector<int> octetsIPBits;
		vector<int> octetsMaskBits;
		getNHBits(octetsIP, octetsMask, octetsIPBits, octetsMaskBits);
		vector<int> netID = getNetID(octetsIP, octetsMask);
		vector<int> decimalNetID = toDecimal(netID, decimals);
		int netInc = getIncrement(decimalMask, decimalNetID);
		cout << endl;

		// вывод ip класса
		cout << "------------------------------------------" << endl;
		cout << "//////////// Информация о классе ///////////" << endl;
		cout << "------------------------------------------" << endl;
		int classResult = calcClass(octetsIP);
		int ipClass = 0;
		switch (classResult) {
		case 1:
			cout << "IP класс: класс 'A', частные адреса" << endl;
			ipClass = 1;
			break;
		case 2:
			cout << "IP класс: класс 'B', частные адреса" << endl;
			ipClass = 2;
			break;
		case 3:
			cout << "IP класс: класс 'C', частные адреса" << endl;
			ipClass = 3;
			break;
		case 4:
			cout << "зарезервированые адреса" << endl;
			ipClass = 1;
			break;
		case 5:
			cout << "IP класс: A" << endl;
			ipClass = 1;
			break;
		case 6:
			cout << "IP класс: B" << endl;
			ipClass = 2;
			break;
		case 7:
			cout << "IP класс: C" << endl;
			ipClass = 3;
			break;
		case 8:
			cout << "IP класс: D" << endl;
			ipClass = 4;
			cout << "!! Это класс IP для мультикаста" << endl;
			break;
		case 9:
			cout << "IP класс: E" << endl;
			ipClass = 5;
			cout << "!! Это зарезервированный IP класс" << endl;
			break;
		default:
			cout << "Не подходит под классы" << endl;
			break;
		}
		vector<int> subClassMask;
		getSubnets(decimalMask, ipClass, subClassMask);
		cout << "Маска подсети по умолчанию: " << toString(subClassMask) << endl;
		cout << "-----------------------------------------" << endl << endl;

		// вывод деталей о подсети //
		cout << "------------------------------------------" << endl;
		cout << "///////////// Детали подсети /////////////" << endl;
		cout << "------------------------------------------" << endl;
		vector<int> netIDRange = getNetIDRange(decimalNetID, netInc, decimalMask);
		cout << "Адрес сети:            -           Широковещательный IP адрес: " << endl;
		cout << "-------------------------------------------------" << endl;
		cout << toString(netID) << " - [ используемые хосты ] - ";
		cout << toString(netIDRange) << endl << endl;
		/*cout << "Network Increment: " << getIncrement(decimalMask, decimalNetID) << endl;
		yh*/
		cout << "Количество подсетей: " << getSubnets(decimalMask, ipClass, subClassMask) << endl;
		cout << "Количество хостов: " << getHostsPerSubnet(decimalMask) << endl;
		cout << "-----------------------------------------" << endl << endl;

		cout << "Хотите ввести еще один IP адрес? (y or n): ";
		cin >> resp;
		cout << endl << endl << endl << endl;
	}
	return 0;
}
