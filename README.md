# freshworks back end development


#include <bits/stdc++.h>
#ifdef _WIN32
#include <Windows.h>
#else
#include <unistd.h>
#endif
using namespace std;

unordered_map<string,pair<int,long long>>store;  //map to store key-value pairs


bool valid(string key){  //method to check whether a given key is in correct format
    for(int i=0;i<key.length();i++){
        int uppercaseChar = toupper(key[i]); //Convert character to upper case version of character
if (uppercaseChar < 'A' || uppercaseChar > 'Z'){ //If character is not A-Z
return false;
}
    }
    return true;
}

void Create(string key,int val,long long time_out=0){
   
    if(store.find(key)!=store.end()){  
        cout<<"ERROR : This key already exists!"<<endl;  //error message 1
        return;
    }
    if(valid(key)){
        if(store.size()<(1024*1024*1024)){  //constraint for file size less than 1GB  
            if(key.length()<=32 && val<(1024*1024*16)){  //constraints for input key_name capped at 32chars and Json object value less than 16KB
                if(time_out==0){
                    pair<int,long long>a=make_pair(val,0);
                    store[key]=a;
                    cout<<"Successfully created"<<endl;  //Successfull creation message
                }
                else{
                    time_t seconds;
                    seconds=time(NULL);
                    pair<int,long long>a=make_pair(val,seconds+time_out);
                    store[key]=a;
                    cout<<"Successfully created"<<endl;  //Successfull creation message
                }
            }
            else{
                cout<<"ERROR : Key size should be less than 32 characters! and Value should be less than 16KB!"<<endl;  ////error message 2
            }
        }
        else{
           cout<<"ERROR : Memory limit exceeded!"<<endl;  ////error message 3
        }
    }
    else{
       cout<<"ERROR : Invalid key name!. key name must contain only alphabets and no special characters or numbers"<<endl;  //error message 4
    }
    return;
}

void Read(string key){
    if(store.find(key)==store.end()){
        cout<<"ERROR : given key does not exist in database. Please enter a valid key"<<endl;  //error message 5
        return;
    }
    time_t seconds;
    seconds=time(NULL);
    if(store[key].second==0 || seconds<=store[key].second){  //comparing the present time with expiry time
        int val=store[key].first;
        string value=to_string(val);
        string ans=key+":"+value;
        cout<<ans<<endl;  //printing value in Json object format
    }
    else{
        cout<<"ERROR : time-to-live of "<<key<<" has expired"<<endl;  //error message 6
    }
}

void Delete(string key){
    if(store.find(key)==store.end()){
        cout<<"ERROR : given key does not exist in database. Please enter a valid key"<<endl;  //error message 7
        return;
    }
    time_t seconds;
    seconds=time(NULL);
    if(store[key].second==0 || seconds<=store[key].second){  //comparing the present time with expiry time
         store.erase(key);
         cout<<"Successfully Deleted"<<endl;  //Successfull deletion message
         return;
    }
    else{
        cout<<"ERROR : time-to-live of "<<key<<" has expired"<<endl;  //error message 8
    }
}


int main() {

//UNIT TESTS DONE:

//1.create a key with correct key_name,value and without time-to-live property, reading it and deleting it.
 Create("Fresh",25);
 Read("Fresh");
 Delete("Fresh");
 
//2.create a key with incorrect key_name.(error 4)
 Create("FreshWorks123isagreatcomapnytoworkin_",25);
 
//3.create a key that already exists.(error 1)
 Create("Fresh",50);
 Create("Fresh",100);
 Delete("Fresh");

//4.read and Delete a key that is not present in database or that has been deleted
 Read("FreshWorks"); //(error 5)
 Read("Fresh"); //(error 5)
 Delete("FreshWorks"); //(error 7)

//5.create a key with time-to-live property and reading and deleting it before time-to-live seconds
 Create("FreshDesk",100,80);
 Read("FreshDesk");
 Delete("FreshDesk");
 
//6.create a key with time-to-live property and reading and deleting it after time-to-live seconds
 Create("FreshDesk",100,4);
 sleep(5); //sleeping the system for 5 seconds at which the key has expired
 Read("FreshDesk"); //(error 6)
 Delete("FreshDesk"); //(error 8)

   
 return 0;
}

