# Search-String-For-Address
auto string searching method


```C++
DWORD TERV_GetCalling(DWORD CALL){
	return (CALL + *(DWORD*)(CALL + 1)) + 5;
}


DWORD TERV_GetNextCall(DWORD from){
	for (int i = 0; i < INT_MAX; i++){
		if (*(BYTE*)(from + i) == 0xE8){
			return from + i;
			break;
		}
	}
	return from;
}

DWORD TERV_GetCallNum(DWORD from, DWORD n){
	int res = 0;
	for (int i = 0; i < INT_MAX; i++){
		if (*(BYTE*)(from + i) == 0xE8){
			res += 1;
			if (res == n){
				return from + i;
				break;
			}
		}
	}
	return from;
}

DWORD TERV_OpNum(char op, DWORD from, DWORD n){
	int res = 0;
	for (int i = 0; i < INT_MAX; i++){
		if (*(BYTE*)(from + i) == op){
			res += 1;
			if (res == n){
				return from + i;
				break;
			}
		}
	}
	return from;
}

DWORD _TERV_GetCallNum(DWORD from, DWORD n){
	return TERV_GetCalling(TERV_GetCallNum(from, n));
}



DWORD TERV_GetFuncTop(DWORD a){
	const char prol[3] = { 0x55, 0x8B, 0xEC };
	for (int i = a; i > INT_MIN; i -= 1){
		if (memcmp(prol, (void*)i, sizeof(prol)) == 0){
			return i;
		}
	}
}

DWORD TERV_GetStringSubLoc(const char* c, int region = 0x400000, int dataRegion = 0x010DF000, int _max = INT_MAX){ //returns 
	DWORD result = NULL;																							//as the function the string is in
	for (int i = dataRegion; i < _max; i = i + 1){																	//so you can just call Terv_GetNextCall
		if (*(const char*)format(i) != NULL){																		//if there are multiple funcs u need
			char* strloc = (char*)format(i);

			if (std::string(strloc) == std::string(c)){
				//printf("Got str: %s\n", strloc);
				result = format(i);
				break;
			}
		}

	}

	for (int i = region; i < _max; i++){
		if (*(BYTE*)format(i) == 0x68 && *(DWORD*)format(i + 1) == result){
			result = format(i);
			break;
		}
	}
	result = TERV_GetFuncTop(result);
	return result;
}
```
