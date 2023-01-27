import java.util.Arrays;

/*
 * David K.
 * CMSC 430
 * HW3 -- THREADS IN JAVA -- 
 */

public class App {

    // initialize global variables
    public static long fang1output;
    public static long fang2output;
    public static long oddfang1output;
    public static long oddfang2output;
    public static int evenCount;
    public static int oddCount;

    public static void main(String args[]) {
        int startInterval = 100000;
        int finalInterval = 999999;

        // create child thread 1
        Thread t1 = new Thread(new Runnable() {
            // thread action on run
            public void run() {
                for (int i = startInterval; i <= finalInterval; i++) {
                    // loop through numbers, if even continue
                    if (i % 2 == 0) {
                        boolean vampire = isVamp(i);
                        // if vampire is found
                        if (vampire) {
                            // output accessing global variables
                            System.out.println("First worker found: " + i + " : [" + App.fang1output + ","
                                    + App.fang2output + "]");
                            App.evenCount = App.evenCount + 1;
                        }
                    }
                }
            }
        });

        // create child thread 1
        Thread t2 = new Thread(new Runnable() {
            // thread action on run
            public void run() {
                System.out.println("inside t1\n");
                for (int i = startInterval; i <= finalInterval; i++) {
                    // loop through numbers, if odd continue
                    if (i % 2 != 0) {
                        boolean vampire = isVamp(i);
                        // if vampire is found
                        if (vampire) {
                            // output accessing global variables
                            System.out.println("second worker found: " + i + " : [" + App.oddfang1output + ","
                                    + App.oddfang2output + "]");
                            App.oddCount = App.oddCount + 1;
                        }
                    }
                }
            }
        });

        try {
            // start t1
            t1.start();

            // start t2, join() threads
            t2.start();
            t1.join();
            t2.join();
        } catch (InterruptedException ie) {
            System.out.println("Interrupted Edxception " + ie);
        }
        System.out.print("\n\nThe fisrt worker found " + App.evenCount + " Vampire Numbers.\n\n");
        System.out.print("The second worker found " + App.oddCount + " Vampire Numbers.\n\n");
        System.out.print("The total number of Vampire numbers found is: " + (App.evenCount + App.oddCount));
    }

    private static boolean isVamp(long num) {
        // if length of number is not even it cannot be a vampire number
        if (numDigits(num) % 2 != 0) {

            return false;
        }

        for (long fang1 = 100; fang1 <= 999; fang1++) {
            if (num % fang1 == 0) {
                long fang2 = num / fang1;

                if (fangCheck(num, fang1, fang2)) {
                    if (num % 2 == 0) {
                        App.fang1output = fang1;
                        App.fang2output = fang2;
                        return true;
                    } else {
                        App.oddfang1output = fang1;
                        App.oddfang2output = fang2;
                        return true;
                    }
                }
            }
        }

        return false;
    }

    private static int numDigits(long num) {
        return Long.toString(Math.abs(num)).length();
    }

    private static boolean fangCheck(long orig, long fang1, long fang2) {
        // check if the last digits of fang1 and fang2 are not both 0
        if (Long.toString(fang1).endsWith("0") && Long.toString(fang2).endsWith("0")) {
            return false;
        }

        // check if the digits of fang1 and fang2 combined are in the same order as the
        // digits of orig
        byte[] origBytes = Long.toString(orig).getBytes();
        byte[] fangBytes = (Long.toString(fang1) + Long.toString(fang2)).getBytes();
        Arrays.sort(origBytes);
        Arrays.sort(fangBytes);
        return Arrays.equals(origBytes, fangBytes);
    }

}