
# Refactoring. 
20210702


### 太小的程序不值得重构。
> 然而坦白说，那个程序的规模根本不值得我们那么做

> 快速而随性地设计一个简单的程序并没有错


### 三次重构

Don Roberts 给了我一条准则: 
第一次做某件事时只管去做; 
第二次做类似的事会产生反感，但无论如何还是可以去做; 
第三次再做类似的事，你就应该重构。  
> 事不过三，三则重构

    public String buildRentInfoString() {
    
        float totalAmount = 0;
        int freqRenterPoints = 0;
        StringBuilder sb = new StringBuilder();
        sb.append("Rental Record for %s\n".format(getName()));
        
        for (Rental r : _rentals) {
            float thisAmount = 0;
    
            switch (r.getMovie().getPriceCode()) {
                case Movie.REGULAR:
                    thisAmount += 2;
                    if (r.getDaysRented() > 2)
                        thisAmount += (r.getDaysRented() - 2) * 1.5f;
                    break;
                case Movie.NEW_RELEASE:
                    thisAmount += r.getDaysRented() * 3;
                    break;
                case Movie.CHILDRENS:
                    thisAmount += 1.5f;
                    if (r.getDaysRented() > 3)
                        thisAmount += (r.getDaysRented() - 3) * 1.5f;
                    break;
            }
    
            freqRenterPoints++;
            
            if ((r.getMovie().getPriceCode() == Movie.NEW_RELEASE) && r.getDaysRented() > 1)
                freqRenterPoints++;
    
            totalAmount += thisAmount;
            sb.append("\t %s \t %s \n".format(r.getMovie().getTitle(), thisAmount));
    
        }
    
        sb.append("Amount owed is %s\n".format(totalAmount));
        sb.append("You earned %s frequent renter points".format(freqRenterPoints));
    
        return sb.toString().
    }

D REFAC

    // this is just Entity-Print func, shouldn't involves any sensitive calc impl.
    // and, this func is pretty randomly/arbitary functional, so the name shoundn't be too short,
    //     use long and arbitray name to cut down the weight.
    // and this is an really arbitrary functional, so I doesn't plan put it in an object member, 
    //     instead it just a total external randomly extension.
    public static String buildCustomerRentInfoString(Customer customer) {
        StringBuilder sb = new StringBuilder();
        sb.append("Rental Record for %s\n".format(customer.getName()));
        
        for (Rental r : customer._rentals) {

            sb.append("\t %s \t %s \n".format(r.getMovie().getTitle(), r.calcRentalAmount()));
        }
    
        sb.append("Amount owed is %s\n".format(customer.calcSumRentalAmount()));
        sb.append("You earned %s frequent renter points".format(freqRenterPoints));
    
        return sb.toString().
    }

    float Rental::calcRentalAmount() {
        float thisAmount = 0;
        switch (r.getMovie().getPriceCode()) {
            case Movie.REGULAR:
                thisAmount += 2;
                if (r.getDaysRented() > 2)
                    thisAmount += (r.getDaysRented() - 2) * 1.5f;
                break;
            case Movie.NEW_RELEASE:
                thisAmount += r.getDaysRented() * 3;
                break;
            case Movie.CHILDRENS:
                thisAmount += 1.5f;
                if (r.getDaysRented() > 3)
                    thisAmount += (r.getDaysRented() - 3) * 1.5f;
                break;
        }
        return thisAmount;
    }

    float Customer::calcSumRentalAmount() {
        float amount = 0;
        for (Rental r : _rentals)
            amount += r.calcRentalAmount();
        return amount;
    }

    int Customer::calcFreqRentalPoints() {
        int freqRentalPoints = 0;
        for (Rental r : _rentals) {
            freqRentalPoints++;

            if ((r.getMovie().getPriceCode() == Movie.NEW_RELEASE) && r.getDaysRented() > 1)
                freqRenterPoints++;
        }
        return freqRentalPoints;
    }
    