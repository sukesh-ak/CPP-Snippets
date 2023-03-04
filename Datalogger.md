# Simple Datalogger C++ class


Try the snippet [here](https://godbolt.org/z/814jb8s7b) powered by `godbolt.org`

```c++
/*
    Simple Data logging class in C++
*/

#include <iostream>
#include <algorithm>
#include <fmt/core.h>

class DataLogger {
    public:

    // Declare the event type using a function signature (in this case, a void function with a string parameter)
    using ErrorEventHandler = std::function<void(const std::string& ErrorString)>;

    // Declare a virtual method to add an event handler to the log event
    virtual void SetErrorEventHandler(ErrorEventHandler handler) = 0;

    // Setup - start file or device
    virtual bool Setup() = 0;   

    // Write log line (for string format)    
    virtual void Log(const std::string& line) = 0;

    // Flush content
    virtual void Flush() = 0;
};

class SDLogger : public DataLogger
{
public:
    // Implement the SetErrorEventHandler method for the derived class
    void SetErrorEventHandler(ErrorEventHandler handler) override
    {
        // set the error event handler
        errorHandler = handler;
    }

    bool Setup() override {
        fmt::print("SD Setup\n");
        return true;
    };

    void Log(const std::string& line) override {
        fmt::print("SD Log {}\n",line);
    };

    void Flush() override {
        fmt::print("SD Flush\n");
        if (errorHandler) errorHandler("SD:Error while flushing");
    };

private:
    // Declare a private variable to hold registered error event handler
    ErrorEventHandler errorHandler;
};

class SerialLogger : public DataLogger
{
public:
    // Implement the SetErrorEventHandler method for the derived class
    void SetErrorEventHandler(ErrorEventHandler handler) override
    {
        // set the error event handler
        errorHandler = handler;
    }

    bool Setup() override {
        fmt::print("Serial Setup\n");
        return true;
    };

    void Log(const std::string& line) override {
        fmt::print("Serial Log {}\n",line);
    };

    void Flush() override {
        fmt::print("Serial Flush\n");
        if (errorHandler) errorHandler("Serial:Error while flushing");
    };

private:
    // Declare a private variable to hold registered error event handler
    ErrorEventHandler errorHandler;
};

void OnError(const std::string& ErrorString){
    fmt::print("{}\n",ErrorString);
}

int main() {
    DataLogger *sdlogger = NULL;
    DataLogger *seriallogger = NULL;
    
    fmt::print("*** SDLogger\n");
    sdlogger = new SDLogger();
    sdlogger->SetErrorEventHandler(OnError);
    sdlogger->Setup();
    sdlogger->Log("Hello world");
    sdlogger->Flush();

    
    fmt::print("\n*** SerialLogger\n");
    seriallogger = new SerialLogger();
    seriallogger->SetErrorEventHandler(OnError);
    seriallogger->Setup();
    seriallogger->Log("Hello world");
    seriallogger->Flush();


    fmt::print("\nDone!");
}


```