# EndPoint

```c
class Endpoint
{
public:
    /** Retrieve the singleton instance of the endpoint */
    static Endpoint &instance() PJSUA2_THROW(Error);

    /** Default constructor */
    Endpoint();

    /** Virtual destructor */
    virtual ~Endpoint();
}
```

