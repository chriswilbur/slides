ClassFactory

```cpp [73: 6|31-38|40|42-44]
/**
 * @brief ClassFactory is a template class that provides a base class for generating factories of a
 * given type. The parameter T provides a definition for the factory class, and CArgs is a
 * list of types for the constructor of class T.
 */
template < class T, typename... CArgs > class ClassFactory
{
public:
  /**
   * @brief The RegisterType function provides the type of a derived type for factory generation
   * @param[in] name is the name associated with the factory
   * @retval true if the derived type is added to the map of factory types
   * @retval false otherwise
   */
  template < typename TDerived > bool RegisterType(std::string const &name)
  {
    // std::map emplace function will not overwrite an existing element. Its
    // returned type of pair<iterator,bool> is used to determine if the
    // insertion was successful or not.
    return funcs_container_.emplace(name, &CreateFunc< TDerived >).second;
  }

  /**
   * @brief The UnregisterType function will remove a named factory from the map of factory types
   * @param[in] name is the name associated with the factory
   * @retval true if the derived type is successfully removed from the map of factory types
   * @retval false otherwise
   */
  bool UnregisterType(std::string const &name) { return (funcs_container_.erase(name) > 0); }

  /**
   * @brief The Create function will create an instance based on the named type in the factory
   * @param[in] name is the name associated with the factory
   * @param[in] args is a list of arguments associated with the constructor of the factory typd
   * @retval pointer to the created instance for the class template parameter type if successful
   * @retval nullptr otherwise
   */
  std::unique_ptr< T > Create(std::string name, CArgs... args)
  {
    auto it = funcs_container_.find(name);
    if (it != funcs_container_.end())
    {
      return it->second(std::forward< CArgs >(args)...);
    }
    return nullptr;
  }

  // Constructor and Destructor
  /**
   * @brief ClassFactory default constructor
   */
  ClassFactory() = default;

  /**
   * @brief ClassFactory default destructor
   */
  virtual ~ClassFactory() = default;

  // Prevent copy construction and assignment
  /**
   * @brief Copy constructor
   */
  ClassFactory(const ClassFactory & /*factory*/) {}
  /**
   * @brief Assignment constructor
   * @return copy of a factory
   */
  ClassFactory &operator=(const ClassFactory & /*factory*/) { return *this; }

private:
  // By using template methods, you avoid the need for a static create method
  // in every descendant class.
  template < typename DerivedType > static std::unique_ptr< T > CreateFunc(CArgs... args)
  {
    return (lmt::make_unique< DerivedType >(std::forward< CArgs >(args)...));
  }

  // type define the function
  using PCreateFunc = std::unique_ptr< T > (*)(CArgs...);

  using CallbackMap = std::unordered_map< std::string, PCreateFunc >;
  CallbackMap funcs_container_;
};
```