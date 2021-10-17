

```c++
/****************************************************************************************
*
*
* @brief 实现单例类宏
****************************************************************************************/

#pragma once

#define DECLARE_SINGLETON(ClassName) \
               public: \
                    static void CreateSingleton();  \
                    static void DestroySingleton(); \
                    static ClassName& Singleton(); \
                    static bool IsSingletonExist(); \
               private:    \
                    ClassName();    \
                    virtual ~ClassName();   \
                    ClassName(const ClassName&);    \
                    ClassName& operator=(const ClassName&); \
                    static ClassName* m_instance;

#define DEFINE_SINGLETON(ClassName, ...) \
               ClassName* ClassName::m_instance = nullptr;  \
			   ClassName::ClassName() { EXTRAC_1st_PARAM(__VA_ARGS__); }    \
			   ClassName::~ClassName() { EXTRAC_2nd_PARAM(__VA_ARGS__); };   \
               \
               void ClassName::CreateSingleton()   \
               {    \
                    check(m_instance == nullptr && TEXT("create more than onece ?"));   \
                    m_instance = new ClassName();  \
               }    \
               \
               void ClassName::DestroySingleton()  \
               {   \
                    check(m_instance != nullptr && TEXT("destroy more than onece ?"));  \
                    if (m_instance) \
                    {   \
                        delete m_instance;  \
                        m_instance = nullptr;   \
                    }   \
               }   \
               \
               ClassName& ClassName::Singleton() \
               {   \
                    check(m_instance != nullptr && TEXT("singleton is not ready to use ?"));    \
                    return *m_instance; \
               } \
               bool ClassName::IsSingletonExist() \
               { \
                   return m_instance != nullptr; \
               }
```

usesage：

```c++
class MOPROJECT_API FMOPolicyGameManager
{
	DECLARE_SINGLETON(FMOPolicyGameManager);
	
	//other code
}
```

