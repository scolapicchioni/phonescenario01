# Day 1
## The problem
> As a **User** I want to bring two phones to the **TechSupport** so that they **Copy** the **Contacts** from one **Phone** to the other 

## Step 1 - Analysis

- Interview the customer to get more details.

> - A *Contact* has a **Name** and a **Phone Number**
> - A **Contact Name** cannot be longer than 20 letters  
> - A *Phone* has a **Brand**, a **Model** and a **Collection of Contacts** 

- Identify the **nouns** and **verbs** in order to find the **elements** (**classes**), the element **characteristics** (**properties**) and the **tasks** (**methods**) 

We decide to create 3 *classes*:
- **Contact**
- **Phone**
- **TechSupport**

**Contact** has 2 *properties*
- **Name**, of type text
- **PhoneNumber**, of type text

**Phone** has 3 *properties*
- **Brand**, of type text
- **Model**, of type text
- **Contacts**, a collection of **Contact**

**TechSupport** has one *method*
- **CopyContacts**, which accepts 2 **Phone** as input and returns no output

![Alt](/cd01_01.png "Class Diagram")

We decide to organize our code into one **Solution** with 2 **Projects**
- **UserInterface**, an *executable* (Console Application)
- **BusinessLogic**, a *library* 

We then add a *reference* from the exe to the dll

![Alt](/pd01_01.png "Class Diagram")


##Step 2 - Write The Code

We first create the **Contact** *class*, since it has no dependency on any other component of the application.
We make it simple and create the properties as **public variables** (*fields*)

```
//Contact.cs
namespace BusinessLogic
{
    /// <summary>
    /// Represents one Contact in an Phone
    /// </summary>
    public class Contact
    {
        /// <summary>
        /// The Full Name of the Contact
        /// </summary>
        public string Name;

        /// <summary>
        /// The Phone Number of the Contact
        /// </summary>
        public string PhoneNumber;
    }
}
```

we can now write some **Tests** to check if our Contact respects the prerequisites.

```
namespace BusinessLogic.Tests
{
    [TestClass]
    public class ContactTest
    {
        [TestMethod]
        public void ContactNameShorterThan20CharactersShouldNotBeTruncated()
        {
            //Arrange
            Contact c = new Contact();
            string name = "ABCDE";

            //Act
            c.Name = name;

            //Assert
            Assert.AreEqual(name.Length, c.Name.Length);
        }

        [TestMethod]
        public void ContactNameAsLongAs20CharactersShouldNotBeTruncated()
        {
            //Arrange
            Contact c = new Contact();
            string name = "12345678901234567890";

            //Act
            c.Name = name;

            //Assert
            Assert.AreEqual(20, c.Name.Length);
        }

        [TestMethod]
        public void ContactNameLongerThan20CharactersShouldBeTruncated()
        {
            //Arrange
            Contact c = new Contact();
            string name = "Daenerys Stormborn of House Targaryen, the Unburnt, Mother of Dragons, khaleesi to Drogo's riders, and queen of the Seven Kingdoms of Westeros";
            //Act
            c.Name = name;

            //Assert
            Assert.AreEqual(20, c.Name.Length);
        }
    }
}
``` 

We **fail** one test. We will fix it later.

 ![Alt](/Tests01_01.png "Failing Tests")

We now proceed to implement the Phone.
We start simple and we build a class with public fields.
We also implement a **constructor** to initialize the list and make room for 100 contacts. 
```
//Phone.cs
namespace BusinessLogic
{
    /// <summary>
    /// A Phone
    /// </summary>
    public class Phone
    {
        /// <summary>
        /// The Brand of the Phone
        /// </summary>
        public string Brand;
        /// <summary>
        /// The Phone Model
        /// </summary>
        public string Model;

        /// <summary>
        /// The Collection of Contacts stored in the Phone
        /// </summary>
        public Contact[] Contacts;

        /// <summary>
        /// Initializes a new Phone with a collection that has room for 100 Contacts
        /// </summary>
        public Phone()
        {
            Contacts = new Contact[100];
        }
    }
}
```

We then implement the **TechSupport** class.
Here we do not write  any *property*, but we do write a *method*: **CopyContacts**.
- We accept as *input* two *parameters* of type **Phone** (*source* and *target*). 
- We give nothing back (*void*).
- We loop through the list of contacts of the *source* phone and for each contact found 
  - we create a new contact
  - we copy the name and phone number into the new contact
  - we reference the newly created contact from the list of contacts of the *target* phone 

```
//TechSupport.cs
namespace BusinessLogic
{
    /// <summary>
    /// The Tech Support. It manages Phones.
    /// </summary>
    public class TechSupport
    {
        /// <summary>
        /// Copies the Contacts from one source Phone to a target
        /// </summary>
        /// <param name="source">The Phone with the source collection of contacts to be copied to the target</param>
        /// <param name="target">The target Phone that will receive the contacts from the source</param>
        public void CopyContacts(Phone source, Phone target) {
            Contact[] sc = source.Contacts;
            Contact[] tc = target.Contacts;

            for (int i = 0; i < sc.Length; i++)
            {
                //we can't do this because it's a reference, not a copy
                //tc[i] = sc[i];

                if (sc[i] != null)
                    tc[i] = new Contact() { Name = sc[i].Name, PhoneNumber = sc[i].PhoneNumber };
            }
        }
    }
}
```

We can finally implement the application.
- We set the scene by creating a couple of phones and initializing them.
- We add some contact to the old phone
- We create the TechSupport component.
- We Let the Tech Support component copy the contacts from our old phone to our new phone
- We loop through the contacts of the new phone to check if everything went as expected

```
//Program.cs
using BusinessLogic;
using System;

namespace UserInterface
{
    /// <summary>
    /// The console application
    /// </summary>
    class Program
    {
        /// <summary>
        /// The starting point of our application
        /// </summary>
        /// <param name="args"></param>
        static void Main(string[] args)
        {
            //initialize the old phone
            Phone oldPhone = new Phone() { Brand = "Apple", Model = "iPhone5" };

            //add some contacts with some details
            oldPhone.Contacts[0] = new Contact() { Name = "Alice Anderson", PhoneNumber = "0612345678" };
            oldPhone.Contacts[1] = new Contact() { Name = "Bob Builders", PhoneNumber = "+39 347 2839654" };
            oldPhone.Contacts[2] = new Contact() { Name = "Candice Clarkson", PhoneNumber = "065707483" };
            oldPhone.Contacts[3] = new Contact() { Name = "David Danielson", PhoneNumber = "060485289" };
            oldPhone.Contacts[4] = new Contact() { Name = "Daenerys Stormborn of House Targaryen, the Unburnt, Mother of Dragons, khaleesi to Drogo's riders, and queen of the Seven Kingdoms of Westeros", PhoneNumber = "063058673" };

            //initialize new phone (empty at the beginning)
            Phone newPhone = new Phone() { Brand = "Samsung", Model = "Galaxy S7" };

            //initialize Tech Support
            TechSupport shopInTheCityCenter = new TechSupport();

            //let the TechSupport work for us, with the old phone as source and the new phone as target
            shopInTheCityCenter.CopyContacts(oldPhone, newPhone);

            //let's see the contacts in the new phone
            for (int i = 0; i < newPhone.Contacts.Length; i++)
            {
                if(newPhone.Contacts[i]!=null)
                    Console.WriteLine($"Contact {i}: {newPhone.Contacts[i].Name} {newPhone.Contacts[i].PhoneNumber}");
            }

            Console.ReadLine();
        }
    }
}

```

## Step 3 - Refactor

There are a couple of problems in our application.
1. The Contact can have a Name longer than 20 characters.

We solve this by creating a **Property** on top of our **field**
- The *field* is now **private** (an application that uses a Contact cannot directly reach it)
- The *property* is **public** and has a **get** and a **set** block
  - The **set** block writes into the private field but only after having checked if the *value* respects the prerequisites.
  - The **get** block returns the value of the field 

We also transform PhoneNumber into a **Property**

```
//Contact.cs
namespace BusinessLogic
{
    /// <summary>
    /// Represents one Contact in an Phone
    /// </summary>
    public class Contact
    {
        //field
        private string name;

        /// <summary>
        /// The Name of the Contact. It cannot be longer than 20 characters.
        /// </summary>
        public string Name
        {
            get { return name; }
            set {
                if (value.Length <= 20)
                    name = value;
                else
                    name = value.Substring(0, 20);
            }
        }

        //auto implemented property
        public string PhoneNumber { get; set; }
    }
}

```

If we now run the tests again we will se that they no longer fail.

 ![Alt](/Tests02_01.png "Passing Tests")
 
 We now want to create a better phone.
 - The Collection of Contacts can directly be accessed, which means that the application could potentially insert a Contact in a random position. We run the risk of overwriting existing contacts and/ or fragmenting the list. 
 - We do not have an indication of how many contacts we have in the list, meaning that 
   - we always have to scroll the whole list to see which contacts we have
   - we don't really know where to add a new contact (what happens if the target phone already has contacts?) 

We decide to refactor our Phone by
- making the collection of contacts **private**
- adding a *counter* to keep track of how many contacts we have in the list
- adding methods to *Create*, *Read*, *Update*, *Delete* (CRUD) the contacts from the list 
- we also **overload** the AddContact method 
 
```
namespace BusinessLogic
{
    /// <summary>
    /// A Phone
    /// </summary>
    public class Phone
    {
        /// <summary>
        /// The Brand of the Phone
        /// </summary>
        public string Brand { get; set; }
        /// <summary>
        /// The Phone Model
        /// </summary>
        public string Model { get; set; }

        /// <summary>
        /// The Collection of Contacts stored in the Phone
        /// </summary>
        private Contact[] contacts;

        /// <summary>
        /// The number of contacts currently in the Phone
        /// </summary>
        public int ContactsCount { get; private set; } = 0;

        /// <summary>
        /// Gets a Contact from the phone
        /// </summary>
        /// <param name="index">The position of the Contact to retrieve</param>
        /// <returns></returns>
        public Contact GetContact(int index)
        {
            return contacts[index]; 
        }

        /// <summary>
        /// Adds a contact in the correct position. 
        /// Eventually increases the size of the list of another 100 places if necessary.
        /// </summary>
        /// <param name="toAdd">The contact to insert in the list</param>
        public void AddContact(Contact toAdd)
        {
            //if we have no more room in the list
            if (ContactsCount >= contacts.Length)
            {
                //we create a temporary list bigger than the previous 
                Contact[] temp = new Contact[contacts.Length + 100];
                for (int i = 0; i < contacts.Length; i++)
                {
                    //we reference all the old contacts from the new list
                    temp[i] = contacts[i];
                }
                //our list is now the new list
                contacts = temp;
            }
            //we add the contact and increment the count
            contacts[ContactsCount++] = toAdd;
        }

        /// <summary>
        /// Creates a new Contact and adds it to the phone
        /// </summary>
        /// <param name="name">The Name of the contact to add</param>
        /// <param name="phoneNumber">The phone number of the contact to add</param>
        public void AddContact(string name, string phoneNumber) {
            Contact newContact = new Contact() { Name = name, PhoneNumber = phoneNumber };
            AddContact(newContact);
        }

        /// <summary>
        /// Initializes a new Phone with a collection that has room for 100 Contacts
        /// </summary>
        public Phone()
        {
            contacts = new Contact[100];
        }
    }
}
```

Of course the TechSupport and the Application do not compile anymore (and that's a good thing).
We refactor them as well.
We begin with the **TechSupport**

```
//TechSupport.cs
namespace BusinessLogic
{
    /// <summary>
    /// The Tech Support. It manages Phones.
    /// </summary>
    public class TechSupport
    {
        /// <summary>
        /// Copies the Contacts from one source Phone to a target
        /// </summary>
        /// <param name="source">The Phone with the source collection of contacts to be copied to the target</param>
        /// <param name="target">The target Phone that will receive the contacts from the source</param>
        public void CopyContacts(Phone source, Phone target) {
            
            for (int i = 0; i < source.ContactsCount; i++)
            {
                Contact oldContact = source.GetContact(i);
                target.AddContact(oldContact.Name, oldContact.PhoneNumber );
            }
        }
    }
}
```

We also refactor the application to use the new methods and properties of the Phone.

```
using BusinessLogic;
using System;

namespace UserInterface
{
    /// <summary>
    /// The console application
    /// </summary>
    class Program
    {
        /// <summary>
        /// The starting point of our application
        /// </summary>
        /// <param name="args"></param>
        static void Main(string[] args)
        {
            //initialize the old phone
            Phone oldPhone = new Phone() { Brand = "Apple", Model = "iPhone5" };

            //add some contacts with some details
            oldPhone.AddContact(new Contact() { Name = "Alice Anderson", PhoneNumber = "0612345678" });
            oldPhone.AddContact(new Contact() { Name = "Bob Builders", PhoneNumber = "+39 347 2839654" });
            oldPhone.AddContact(new Contact() { Name = "Candice Clarkson", PhoneNumber = "065707483" });
            oldPhone.AddContact(new Contact() { Name = "David Danielson", PhoneNumber = "060485289" });
            oldPhone.AddContact(new Contact() { Name = "Daenerys Stormborn of House Targaryen, the Unburnt, Mother of Dragons, khaleesi to Drogo's riders, and queen of the Seven Kingdoms of Westeros", PhoneNumber = "063058673" });

            //initialize new phone (empty at the beginning)
            Phone newPhone = new Phone() { Brand = "Samsung", Model = "Galaxy S7" };

            //initialize Tech Support
            TechSupport shopInTheCityCenter = new TechSupport();

            //let the TechSupport work for us, with the old phone as source and the new phone as target
            shopInTheCityCenter.CopyContacts(oldPhone, newPhone);

            //let's see the contacts in the new phone
            for (int i = 0; i < newPhone.ContactsCount; i++)
            {
                Contact item = newPhone.GetContact(i);
                Console.WriteLine($"Contact {i}: {item.Name} {item.PhoneNumber}");
            }

            Console.ReadLine();
        }
    }
}
```