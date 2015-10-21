# Saving-Account

package store;

import java.io.*;

import java.util.Scanner;
import java.util.Random;
import java.util.TreeMap;
import java.util.Map;

public class Account 
{
	public static void main (String [] args)
	{
		long accNum;
		String accHolder = "", accHolderIC ="";
		double accBalance;
		
		String accHolderDetail, accNumDetail;
		TreeMap <String,String> account = new TreeMap <String,String>();
		
		//Set default path for data to store in the user's home directory. 
		final String FILE_NAME = ".account_database";
		File userHomeDirectory = new File (System.getProperty("user.home"));
		File dataFile = new File (userHomeDirectory, FILE_NAME);
		
		//Check or search existing data.
		if (!dataFile.exists())
		{
			System.out.println ("No existing account file found.");
			System.out.println ("New account file will create on following path.");
			System.out.println (dataFile.getAbsolutePath());
		}
		else
		{
			System.out.println("Reading account data");
			try
			{
				Scanner existing = new Scanner (dataFile);
				while (existing.hasNextLine())
				{
					String accountEntry = existing.nextLine();
					int separator1 = accountEntry.indexOf('|');
					if (separator1 == -1)
					{
						throw new IOException("File is not a account data file.");
					}
					else
					{
						accHolderDetail = accountEntry.substring(0, separator1);
						int separator2 = accHolderDetail.indexOf('$');
						accHolder = accHolderDetail.substring(0,separator2);
						accHolderIC = accHolderDetail.substring(separator2 + 1);
						
						accNumDetail = accountEntry.substring(separator1 + 1);
						int separator3 = accNumDetail.indexOf('$');
						accNum = Long.parseLong(accNumDetail.substring(0, separator3));
						accBalance = Double.parseDouble(accNumDetail.substring(separator3 + 1));
						account.put(accHolderDetail, accNumDetail);
					}
				}
			}
	        catch (IOException Exit) 
	        {
	            System.out.println("Error in account data file.");
	            System.out.println("Path:  " + dataFile.getAbsolutePath());
	            System.out.println("This program cannot continue.");
	            System.exit(1);
	        }
			
			
		}
				
		//Menu screen.
		int option,action;
		Scanner input = new Scanner(System.in);
		boolean changed = false;
		boolean subloop;

		mainloop: while (true)
		{
			System.out.println ("\nSelect the action that you want to perform:");
			System.out.println ("   1.  Create a saving account");
			System.out.println ("   2.  Inquiry a saving account");
			System.out.println ("   3.  Withdraw or Deposit");
			System.out.println ("   4.  Delete a saving account");
			System.out.println ("   5.  Exit from program");
			System.out.println ("Enter action number (1-5):");
			if (input.hasNextInt())
			{
				option = input.nextInt();
				input.nextLine();
			}
			else
			{
				System.out.println ("\nYou must enter a number");
				input.nextLine();
				continue;
			}
			
			switch (option)
			{
				case 1:
				{
					subloop = true;
					while (subloop)
					{
						System.out.println ("\nPlease enter your name:");
						accHolder = input.nextLine().trim().toLowerCase();
						if (accHolder.length() == 0)				
						{
							System.out.println ("\nName cannot be blank.");	
						}
						else if ((accHolder.indexOf('|') >= 0) || (accHolder.indexOf('$') >= 0))
						{
							System.out.println ("\nCannot use '|' or '$' to create as part of your name.");	
						}
						else
							subloop = false;
					}
					
					subloop = true;
					while (subloop)
					{
						System.out.println ("\nPlease enter your IC: ");
						accHolderIC = input.nextLine();
						if (accHolderIC.length() == 0)
						{
							System.out.println ("\nIC cannot be blank.");				
						}
						else if ((accHolderIC.indexOf('-') >= 0)||
								 (accHolderIC.indexOf('$') >= 0)||
							 	 (accHolderIC.indexOf('|') >= 0)||
							 	 (accHolderIC.indexOf(' ') >= 0))
						{
							System.out.println ("\nCannot contain spaces or '-' or '$' or '|' as part of your IC.");				
						}
						else
							subloop = false;
					}
					
					subloop = true;
					while (subloop)
					{
						System.out.println ("\nPlease enter your initial balance");
						if (input.hasNextDouble())
						{
							accBalance = Double.parseDouble(input.nextLine());
							
							Random rand = new Random();
							accNum = Math.abs((long) (rand.nextDouble() * 1000000000000l));
							System.out.println ("\nYour new account number is " + accNum);
							System.out.println ("Your initial balance is " + accBalance);
							
							accHolderDetail = accHolder + "$" + accHolderIC;
							accNumDetail = accNum + "$" + String.valueOf(accBalance);
							account.put(accHolderDetail, accNumDetail);
							subloop = false;
						}
						else
							{
								System.out.println ("This is not an amount.");
								input.nextLine();
								continue;
							}
					}
					changed = true;	
					break;
				}
				case 2:
				{
					System.out.println ("\nTo look up your account number.");
					System.out.println ("Please enter your name ");
					accHolder = input.nextLine().trim().toLowerCase();
					System.out.println ("Please enter your IC ");
					accHolderIC = input.nextLine();
					
					accHolderDetail = accHolder + '$' + accHolderIC;
					accNumDetail = account.get(accHolderDetail);

					if (accNumDetail == null)
					{
						System.out.println ("\nSorry, no account number found under name of " + '"' + accHolder +
								'"' + " and IC of " + '"' + accHolderIC + '"');
					}
					else
					{				
						int separator = accNumDetail.indexOf('$');
						accNum = Long.parseLong(accNumDetail.substring(0, separator));
						accBalance = Double.parseDouble(accNumDetail.substring(separator + 1));
						System.out.println ("\nAccount holder name: " + accHolder);
						System.out.println ("Account holder IC: " + accHolderIC);
						System.out.println ("Account number: " + accNum);
						System.out.println ("Account balance: RM" + accBalance);
					}
					break;	
				}
				case 3:
				{
					System.out.println ("To withdraw or deposit.");
					System.out.println ("Please enter your name ");
					accHolder = input.nextLine().trim().toLowerCase();
					System.out.println ("Please enter your IC ");
					accHolderIC = input.nextLine();

					accHolderDetail = accHolder + '$' + accHolderIC;
					
					accNumDetail = account.get(accHolderDetail);
					if (accNumDetail == null)
					{
						System.out.println ("\nSorry, no account number found under name of " + '"' + accHolder +
								'"' + " and IC of " + '"' + accHolderIC + '"');
						System.out.println ("Transaction ended.");
					}
					else
					{
						int separator = accNumDetail.indexOf('$');
						accNum = Long.parseLong(accNumDetail.substring(0, separator));
						accBalance = Double.parseDouble(accNumDetail.substring(separator + 1));
						System.out.println ("Account holder name: " + accHolder);
						System.out.println ("Account holder IC: " + accHolderIC);
						System.out.println ("Account number: " + accNum);
						System.out.println ("Account balance: RM" + accBalance);
						System.out.println ("\nSelect the action that you want to perform:");
						System.out.println ("   1.  Withdraw");
						System.out.println ("   2.  Deposit");
						System.out.println ("Enter action number (1 or 2):");	
						action = input.nextInt();
						if (action == 1)
						{
							System.out.println ("Please enter amount you want to withdraw");
							input.nextLine();
							accBalance = accBalance - Double.parseDouble(input.nextLine());
						}
						else if (action == 2)
						{
							System.out.println ("Please enter amount you want to deposit.");
							input.nextLine();
							accBalance = accBalance + Double.parseDouble(input.nextLine());
						}
						accNumDetail = accNum + "$" + String.valueOf(accBalance);
						account.put(accHolderDetail, accNumDetail);
						System.out.println ("Account summary");
						System.out.println ("Account holder name: " + accHolder);
						System.out.println ("Account holder IC: " + accHolderIC);
						System.out.println ("Account number: " + accNum);
						if (action == 1)
							System.out.println ("Account balance after withdraw: RM" + accBalance);
						else if (action == 2)
							System.out.println ("Account balance after deposit: RM" + accBalance);
						
						changed = true;
					}
					break;
				}				
				case 4:
				{
					System.out.println ("\nTo remove your account.");
					System.out.println ("Please enter your name ");
					accHolder = input.nextLine().trim().toLowerCase();
					System.out.println ("Please enter your IC ");
					accHolderIC = input.nextLine();
					
					accHolderDetail = accHolder + '$' + accHolderIC;
					
					accNumDetail = account.get(accHolderDetail);
					if (accNumDetail == null)
					{
						System.out.println ("\nSorry, no account number found under name of " + '"' + accHolder +
								'"' + " and IC of " + '"' + accHolderIC + '"');
						System.out.println ("Transaction ended.");
					}
					else
					{
						account.remove(accHolderDetail);
						changed = true;
						System.out.println ("\nYour account has been removed from system.");
					}
					break;
				}

				case 5:
				{
					System.out.println ("\nProgram exited.");
					break mainloop;
				}
		        default:
		        {
		             System.out.println("\nILLEGAL ACTION NUMBER.");
		             break;
		        }
			}
		}
		
		if (changed) 
	      {
	          System.out.println("Saving account details changes to file " + 
	                dataFile.getAbsolutePath() + " ...");
	          PrintWriter out;
	          try 
	          {
	             out = new PrintWriter( new FileWriter(dataFile) );
	          }
	          catch (IOException error) 
	          {
	             System.out.println("ERROR: Can't open data file for output.");
	             return;
	          }
	          for ( Map.Entry<String,String> entry : account.entrySet() )
	             out.println(entry.getKey() + "|" + entry.getValue() );
	          out.close();
	          if (out.checkError())
	             System.out.println("ERROR: Some error occurred while writing data file.");
	          else
	             System.out.println("Done.");
	      }  
	}
}

