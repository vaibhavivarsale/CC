Step 1: Create Custom Object

Go to:

Setup → Object Manager → Create → Custom Object

Create:

Property	Value
Label	Product Inventory
Object Name	Product_Inventory
Record Name	Product Name (Text)
Step 2: Create Fields

Inside:

Object Manager → Product Inventory → Fields & Relationships → New

Create these fields:

Field Label	API Name	Data Type
Product Name	Name	Text
Serial No	Serial_No__c	Number(10,0)
Manufacture Date	Manufacture_Date__c	Date
Expiry Date	Expiry_Date__c	Date

Same field creation process as the Student object shown in your file.

Step 3: Open Developer Console

Go to:

⚙ → Developer Console

As shown in your reference file.

----------------------------------------------------------------------------------
Step 4: Apex Class

Create new Apex Class:

ProductInventoryManagement


public class ProductInventoryManagement {

    // Add Product
    public static void addProduct(
        String productName,
        Integer serialNo,
        Date mfgDate,
        Date expDate
    ) {

        Product_Inventory__c p =
            new Product_Inventory__c();

        p.Name = productName;
        p.Serial_No__c = serialNo;
        p.Manufacture_Date__c = mfgDate;
        p.Expiry_Date__c = expDate;

        insert p;
    }


    // Display Products
    public static List<Product_Inventory__c>
    displayProducts() {

        return [

            SELECT Name,
                   Serial_No__c,
                   Manufacture_Date__c,
                   Expiry_Date__c

            FROM Product_Inventory__c
        ];
    }


    // Search Product
    public static List<Product_Inventory__c>
    searchProduct(Integer serialNo) {

        return [

            SELECT Name,
                   Serial_No__c,
                   Manufacture_Date__c,
                   Expiry_Date__c

            FROM Product_Inventory__c

            WHERE Serial_No__c = :serialNo
        ];
    }


    // Update Expiry Date
    public static void updateExpiry(
        Integer serialNo,
        Date newExpiry
    ) {

        Product_Inventory__c p = [

            SELECT Id,
                   Expiry_Date__c

            FROM Product_Inventory__c

            WHERE Serial_No__c = :serialNo

            LIMIT 1
        ];

        p.Expiry_Date__c = newExpiry;

        update p;
    }


    // Delete Product
    public static void deleteProduct(
        Integer serialNo
    ) {

        Product_Inventory__c p = [

            SELECT Id

            FROM Product_Inventory__c

            WHERE Serial_No__c = :serialNo

            LIMIT 1
        ];

        delete p;
    }
}



---------------------------------------------------------------------------------


ProductInventoryController


public class ProductInventoryController {

    // Fields
    public String productName {get; set;}
    public Integer serialNo {get; set;}
    public Date manufactureDate {get; set;}
    public Date expiryDate {get; set;}

    public String selectedAction {get; set;}

    public List<Product_Inventory__c>
        productList {get; set;}


    // Constructor
    public ProductInventoryController() {

        selectedAction = 'Add';
        productList =
            new List<Product_Inventory__c>();
    }


    // Execute
    public void executeAction() {

        if(selectedAction == 'Add') {

            addProduct();
        }

        else if(selectedAction == 'Search') {

            searchProduct();
        }

        else if(selectedAction == 'Update') {

            updateExpiry();
        }

        else if(selectedAction == 'Delete') {

            deleteProduct();
        }

        else if(selectedAction == 'Display') {

            displayProducts();
        }
    }


    // Add
    public void addProduct() {

        ProductInventoryManagement.addProduct(
            productName,
            serialNo,
            manufactureDate,
            expiryDate
        );

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Product Added Successfully'
            )
        );

        clearFields();
    }


    // Display
    public void displayProducts() {

        productList =
            ProductInventoryManagement
            .displayProducts();
    }


    // Search
    public void searchProduct() {

        productList =
            ProductInventoryManagement
            .searchProduct(serialNo);
    }


    // Update
    public void updateExpiry() {

        ProductInventoryManagement
            .updateExpiry(
                serialNo,
                expiryDate
            );

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Expiry Updated Successfully'
            )
        );

        clearFields();
    }


    // Delete
    public void deleteProduct() {

        ProductInventoryManagement
            .deleteProduct(serialNo);

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Product Deleted Successfully'
            )
        );

        clearFields();
    }


    // Clear
    public void clearFields() {

        productName = '';
        serialNo = null;
        manufactureDate = null;
        expiryDate = null;
    }
}


-------------------------------------------------------------------------------------


visual force

<apex:page controller="ProductInventoryController">

    <apex:form>

        <apex:pageMessages id="msg"/>

        <apex:pageBlock title="Product Inventory Management"
            id="mainBlock">

            <apex:pageBlockSection columns="1">

                <apex:selectList
                    value="{!selectedAction}"
                    size="1">

                    <apex:actionSupport
                        event="onchange"
                        reRender="mainBlock"/>

                    <apex:selectOption
                        itemValue="Add"
                        itemLabel="Add Product"/>

                    <apex:selectOption
                        itemValue="Search"
                        itemLabel="Search Product"/>

                    <apex:selectOption
                        itemValue="Update"
                        itemLabel="Update Expiry"/>

                    <apex:selectOption
                        itemValue="Delete"
                        itemLabel="Delete Product"/>

                    <apex:selectOption
                        itemValue="Display"
                        itemLabel="Display Products"/>

                </apex:selectList>

            </apex:pageBlockSection>


            <!-- ADD -->

            <apex:pageBlockSection
                columns="2"
                rendered="{!selectedAction='Add'}">

                <apex:inputText
                    value="{!productName}"
                    label="Product Name"/>

                <apex:inputText
                    value="{!serialNo}"
                    label="Serial No"/>

                <apex:inputField
                    value="{!manufactureDate}"
                    label="Manufacture Date"/>

                <apex:inputField
                    value="{!expiryDate}"
                    label="Expiry Date"/>

            </apex:pageBlockSection>


            <!-- SEARCH -->

            <apex:pageBlockSection
                columns="1"
                rendered="{!selectedAction='Search'}">

                <apex:inputText
                    value="{!serialNo}"
                    label="Serial No"/>

            </apex:pageBlockSection>


            <!-- UPDATE -->

            <apex:pageBlockSection
                columns="2"
                rendered="{!selectedAction='Update'}">

                <apex:inputText
                    value="{!serialNo}"
                    label="Serial No"/>

                <apex:inputField
                    value="{!expiryDate}"
                    label="New Expiry Date"/>

            </apex:pageBlockSection>


            <!-- DELETE -->

            <apex:pageBlockSection
                columns="1"
                rendered="{!selectedAction='Delete'}">

                <apex:inputText
                    value="{!serialNo}"
                    label="Serial No"/>

            </apex:pageBlockSection>


            <apex:pageBlockButtons>

                <apex:commandButton
                    value="Execute"
                    action="{!executeAction}"
                    reRender="table,mainBlock,msg"/>

            </apex:pageBlockButtons>

        </apex:pageBlock>


        <apex:pageBlock
            title="Product Records"
            id="table">

            <apex:pageBlockTable
                value="{!productList}"
                var="p">

                <apex:column
                    value="{!p.Name}"
                    headerValue="Product Name"/>

                <apex:column
                    value="{!p.Serial_No__c}"
                    headerValue="Serial No"/>

                <apex:column
                    value="{!p.Manufacture_Date__c}"
                    headerValue="Manufacture Date"/>

                <apex:column
                    value="{!p.Expiry_Date__c}"
                    headerValue="Expiry Date"/>

            </apex:pageBlockTable>

        </apex:pageBlock>

    </apex:form>

</apex:page>

