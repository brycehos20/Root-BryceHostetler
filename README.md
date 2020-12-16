# Root-BryceHostetler

Assumptions:
- A reasonable file path for the user to enter is "data/driver.txt"
- All driver first names are unique
- All drivers who record trip data are first registered
- The text from any provided input file is consistent with that of the prompt given
  for this problem (i.e "Driver Dan" or "Trip Dan 00:00 00:00 0.0")
- No separate driver trip records have elpased time/distance traveled within 0.1 units     of each other (i.e. "Trip Dan 01:00 01:30 22.3" and "Trip Liam 02:12 3:45 22.3" are assumed to both not be recorded trips because the epsilon between their distances is 0 < 0.1)"

For my implementation of the given problem, I decided to use a Map with keys of type String and values of type Double arrays. After considereing some other possibilities, I thought that this would be the best way to store data in attached elements so I could update trip information about a registered driver when need be. I stored the first names of drivers as keys, and I stored the elapsed driving time and distance traveled as elements in the Double array which represented the value of each map entry. Below, I will explain the implementation behind each of my program methods. Afterwards, I'll discuss the test cases and helper methods that I used to ensure my code was working correctly.


dataExtraction(BufferedReader input):

The purpose of this method is to create the map that consists of all provided information about drivers that are registered. The parameter "input" contains the input stream of a file that has information about registered drivers, so I used a try-catch block to loop over this information. I read in the data line by line, and this process continued until there was no more data to be read from the file (if there was an error processing data for some reason, the catch block would notify the client about said error). For each line of data, I checked the first character of the string. There were only two possible letters that could be seen at the start of the string, ad they are as follows:

If the first character was equal to 'D', then I knew the intention of the line was to register a driver. Registry of a driver required the addition of a new pair into the map of driving records, so I created a substring of of the line being processed and extracted the driver's name. I then created a new Double array of length 2 (NOTE: Double[] was used for the map pairs instead of double[] because it is a wrapper type) and initialized both elements in the array as 0.0. After I had created both the key and value for a the new driver, I added these elements as a new map entry into the driver data map.

If the first charater was equal to 'T' (or any character other than 'D'), then I would extract the name from this string and call upon this name in the driver data map to update their driving records. I would then use the start and end time as well as the distance (all displayed after the driver name) to update the current driving records of the driver whose trip is currently being processed. This updating was done using the updateStats and hourMin methods (explained next). After all data was processed, the dataExtraction method returned the map containing all driving records from the BufferedReader file.


updateStats(String driver, String data, Map<String, Double[] records):

The purpose of this method was to update driving statistics of all registered drivers. This was done by extracting specific characters from the line of data ("Trip Dan ...")
and parsing these values as ints and doubles (all ints would be convereted to doubles). These specific characters are always in the same location of each input line, and their index constants are provided in my implementation of the problem statement. The specific doubles would then be added to the time and distance indices in the map value respresented as type Double[] for the driver whose trip was being processed. However, this data would only be concatenated onto the driver's current records if the average speed of the trip was in between 5 mph and 100 mph.


hourMin(int dig1, int dig2, String info):

The purpose of this method was to parse values into integers given by the current input string. The parameter dig1 represented the index of a character that could be parsed as a number 0-9, and "info" represented the lien of data that contained the number of index dig1. The method contained an if-else that checked for leading zeros, and this if-else block would properly parse the integer data provided in info based on the placement of any potential zeros in the processed string. Once finished, the method returned the parsed integer from the argument "info".


displayRecords(Entry<String, Double[] driver):

The purpose of this method is was to display the records on a certain driver. This was done by getting the key from the argument "driver" (driver name) and rounding the double values from the value of "driver" (time and distance). These rounded values were displayed as longs representing total time traveled and average speed in mph (displayed as "Dan: 34 miles @ 20 mph"). If both double values were equivalent to 0, then the method would simply display "[driver name]: 0 miles".


Main method:
The main method first created a scanner object using command line input, then used this scanner to allow the client to enter the file path to their desired driving records. After the input file was processed successfully (passes try-catch block), the main method then created the driving record map using the dataExtraction method. Once the map was created, the method would attempt to close the BufferedReader input using a try-catch block. Lastly, a view of the driving records map was created using a set of type Entry<String, Double[]>, and an interator was made from this view set of the driving records map. This iterator was used to scan over each Entry in the view set, and each entry was used as an argument in the calling of the displayRecords method.

---------------

JUnit Testing:

To test my program, I used the "none, one, some" process while creating specific test cases. To clarify, I created separate text files with none, one, and some recordings of trips from registered drivers then created test cases for each data file. For the dataExtraction method, the different types of errors that I checked for are as follows:

- One registered driver w/ no trips
- One registered driver w/ a trip
- Multiple registered drivers w/ no trips
- Multiple registered drivers w/ some trips
- Drivers w/ trips that are outside the average speed range of 5 mph and 100 mph
- Drivers that have more than one trip recorded under their name

To test these attributes of each data file, I had to create helper methods which would simplify the process of asserting equality and creating an expected map. The helper methods are as follows:


createFromArgs(String... args):

This method had an undefined amoutn of parameters, and it would use the arbitrary amount parameters to create the key and map values in an expected map. It was the client's job (me) to provide the correct arguments that would put a valid driver name and array of time/distance doubles into an entry in the expected map.


dataChecker(Map<String, Double[]> rounder, Map<String, Double[]> rounded):

The purpose of this method was to individually compare each value from the map processed by a file and the expected map. An iterator was created from a view set of the parameter "rounder", and each entry in the iterator was checked for equality of the values. However, before each double array was compared, it was necessary to assert that each map parameter contained the driver name whose data was being compared. If this assertion was false, then the test case would fail. If true, then the double arrays of "rounder" and "rounded" were compared using the assertArrayEquals method. This method compared the indices of double arrays and asserted true if all indices were within a certain error range of each other (NOTE: the Double[] arrays in the map entries had to be copied as double[] arrays to succesfully use the assertArrayEquals method, and these double[] arrays went out of scope once a new iteration began). If the assertArrayEquals method was false at any point, then the test case failed.


reader(String file):

This method simply created a BufferedReader object and returned the object to the test case that called this method.


The helper methods listed above were used mainly for the comparison between two maps, but other methods such as hourMin and displayRecords were tested as well. For hourMin, four different test cases were implemented: All zeros, leading zero w/ non-zero trailing digit, leading non-zero and trailing zero, and no zeros. For displayRecords, only two cases were implemented. One was to check the output of a registered driver with no valid trips, and the other was to check the routine case of a registered driver with recorded trips. The updateStats method was not tested becuase all of its functionalities wree tested with dataExtraction test cases.
