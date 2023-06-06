import java.util.Scanner;
import java.io.File;
import java.io.FileNotFoundException;
import java.util.ArrayList;

/**
   Pyramid Descent Puzzle Solver
   
   Given a target multiple, PyramidDescentPuzzleSolver finds the target by descending down rows, multiplying each value
   until the target is found. The path taken to the first target found is returned as a String, where each step taken is
   either a "L" or a "R" depending on the value iterated.
   
   Eg. in Pyramid1.txt
   Target: 64
   1,
   2,5
   3,4,1
   6,3,2,2
   4,1,4,2,5
   
   getDescentToTarget( "Pyramid1.txt" ) returns "LRRL"
   
   @author Peter Olson
*/

public class PyramidDescentPuzzleSolver {

   private static Scanner scanner = new Scanner(System.in);

   public static void main( String[] args ) {
      SOPln("What is the name of the pyramid text file you want to use?");
      String fileName = scanner.nextLine();
      SOPln("\nThe path to reach the target value is: " + getDescentToTarget( fileName ) );
   }

   /**
      Finds the descent path of a pyramid of numbers, the values of which multiply to a given target.
      
      The pyramid is stored in a .txt file, where the first row is stated as "Target: #", where # is an integer.
      The first row has one number, and each of the following rows has one more number than the one before it,
      each row delimited by commas.
      
      If the target cannot be reached through any path, then the empty quote is returned ""
      
      ** This algorithm to find the descent path uses binary conversion. I wanted to implement a non-recursive method, and found that
         the best way to do it was by recognizing that a pre-order traversal mimics counting in binary, where 0s represent left path
         choices and 1s represent right path choices. By storing data in a 2D array, you can traverse correctly by converting the
         bit path to zeros and ones and using those for indices, along with a counter to keep track of left to right movement, if you
         were to think of each node as being in a column. My first thought was to just work with a binary tree and traverse, but I
         didn't want to use any packages outside of Java's internal library if I didn't have to, so I went with a more linear solution.
      **
      
      Eg. in Pyramid2.txt
      Target: 720
      2
      4,3
      3,2,6
      2,9,5,2
      10,5,2,15,5
      
      Output: "LRLL"
      
      @param fileName The name of the text file
      @return String The descent path of this pyramid, eg. "RRLLRLR". If there is no path, return ""
   */
   public static String getDescentToTarget( String fileName ) {
      //Make sure fileName has the right extension
      if( !fileName.contains(".txt") )
         fileName += ".txt";
      
      //Path, Data Structure, Pyramid Height
      String descentPath = "";
      ArrayList<String[]> pyramidData = new ArrayList<String[]>();
      int pyramidHeight = 0;
      
      //Set target and read data from file
      Scanner fileSc = getScanner( fileName );
      int target = Integer.parseInt( fileSc.nextLine().split(" ")[1] );
      while( fileSc.hasNextLine() ) {
         String[] line = fileSc.nextLine().split(",");
         pyramidData.add( line );
         pyramidHeight++;
      }
      fileSc.close();
      
      //Find descent path
      int totalPaths = (int)Math.pow( 2, pyramidHeight - 1 );
      for( int pathNum = 0; pathNum < totalPaths; pathNum++ ) {
         //Convert path to zeros and ones, as a String. This will be the directions for traversing the pyramid data
         String bitStringPath = getStringPath( pathNum, pyramidHeight );
         char[] bitCharPath = bitStringPath.toCharArray();
         
         int col = 0;
         int totalValue = 1;
         for( int row = 0; row < pyramidHeight; row++ ) {
            //Get value and multiply
            int value = Integer.parseInt( pyramidData.get( row )[ Character.getNumericValue( bitCharPath[row] ) + col ] );
            totalValue *= value;

            //Check if col should be updated (if bit direction is a 1)
            int bitDirection = Character.getNumericValue( bitCharPath[ row ] );
            if( bitDirection == 1 )
               col++;
         }
         
         //Check if target has been found
         if( totalValue == target ) {
            descentPath = bitStringPath.replace("0", "L").replace("1", "R");
            //Return the path. Do not include the first char, which designates the position of the first number in row 1 (it will always be "L")
            return descentPath.substring(1);
         }
      }
      
      //No path exists if this point is reached
      return descentPath;
   }

   /**
      Convert a number to its binary equivalent. The length of the path should be equal to the pyramid height
      
      @param pathNum The number to convert. Should be a positive number
      @param pyramidHeight The height of the pyramid
      @return String The path as a String in zeros and ones. The total chars should be equal to pyramidHeight
   */
   private static String getStringPath( int pathNum, int pyramidHeight ) {
      String bitStringPath = Integer.toBinaryString( pathNum );
      int length = bitStringPath.length();
      if( length < pyramidHeight )
         bitStringPath = new String( new char[ pyramidHeight - length ] ).replace("\0", "0") + bitStringPath;
         
      return bitStringPath;
   }

   /** Get a Scanner object that is reading a File based on a File name */
   private static Scanner getScanner( String fileName )
   { return getScanner( new File( fileName ) ); }
   /**
      Get a Scanner object that is reading a File
      
      @param file The file to scan
      @return Scanner The scanner object that is reading the given file
   */
   private static Scanner getScanner( File file ) {
      Scanner sc = null;
      try {
         sc = new Scanner( file );
      } catch( FileNotFoundException e ) {
         e.printStackTrace();
      }
      
      return sc;
   }
   
   private static void SOP( String str ) {
      System.out.print( str );
   }
   private static void SOPln() {
      System.out.println();
   }
   private static void SOPln( String str ) {
      System.out.println( str );
   }

}