//+------------------------------------------------------------------+
//|                                                    SabeKadar.mq5 |
//|                                    Copyright 2024, Codexbiz.LTD. |
//|                                                         Codex.biz|
//+------------------------------------------------------------------+
#property copyright "Copyright 2024, MetaQuotes Ltd."
#property link      "https://www.mql5.com"
#property version   "1.00"
//+------------------------------------------------------------------+
//| Expert initialization function                                   |
//+------------------------------------------------------------------+
#include <Trade\Trade.mqh> 

//===========================

//Before the main function
CTrade trade;
double ZigZagArray[], ZigZagArray2[], ZigZagAarry3[] ,zigzag[100], zigzag2[100];
int ZigZagHandle, ZigZagHandle2;
double Ask;
double prev2A = 0.00;
double prev2B = 0.00;



bool orderPlaced = true;





int handleBB;


int OnInit()
  {
//---
   CheckForZigZag();
//Print as many as you want. for example 2 value here
   Print(zigzag[0],zigzag[1]);
   EventSetTimer(1); // 30 seonds in seconds
   

//---
   return(INIT_SUCCEEDED);
  }
//+------------------------------------------------------------------+
//| Expert deinitialization function                                 |
//+------------------------------------------------------------------+
void OnDeinit(const int reason)
  {
//---
   
  }
//+------------------------------------------------------------------+
//| Expert tick function                                             |
//+------------------------------------------------------------------+

void onTick(){
//Calling the function
CheckForZigZag();
//Print as many as you want. for example 2 value here
Print(zigzag[0],zigzag[1]); 
}





//ALL FUNCTION RUN UNDER THIS TIMER.
//==================================//
void OnTimer()
{


double volume = 0.1; // Volume of the sell order
double price = SymbolInfoDouble(_Symbol, SYMBOL_ASK); // Current ask price
double sl = 50; // Stop loss in points
double tp = 100; // Take profit in points



 CheckForZigZag();
 CheckForZigZagUp2();

// CHECK IF A OR B HAS CHANGED OR NOT
static double prevA = zigzag[1];
static double prevB = zigzag2[1];



if (zigzag[1] != prevA)
{
    if (zigzag[1] != 0) // Assuming zigzag[1] is a boolean or indicator of change
    {
      trade.Sell(0.01);
    }  
     else 
     { 
      //double stopLossPrice = SymbolInfoDouble(_Symbol, SYMBOL_BID) + 100 * SymbolInfoDouble(_Symbol, SYMBOL_POINT);
      // A HAS TURNED ON
      //trade.Sell(volume, price, sl, tp, "Sell Order");
      
    }
    // UPDATE PREVIOUS VALUE OF A.
    prevA = zigzag[1];
}



if (zigzag2[1] != prev2A)
{
    if (zigzag2[1] != 0) // Assuming zigzag[1] is a boolean or indicator of change
    {
      
    }  
     else 
     { 
     
      
     }
    
    prev2A = zigzag[1];
}

//Close all possition when up sign value has get
if (zigzag2[0] != prev2B)
{
    if (zigzag[0] != 0) // Assuming zigzag[1] is a boolean or indicator of change
    {
      closeAllPositions();
    }  
     else 
     { 
      //double stopLossPrice = SymbolInfoDouble(_Symbol, SYMBOL_BID) + 100 * SymbolInfoDouble(_Symbol, SYMBOL_POINT);
      // A HAS TURNED ON
      //trade.Sell(volume, price, sl, tp, "Sell Order");
      
    }
    // UPDATE PREVIOUS VALUE OF A.
    prev2B = zigzag[0];
}





}
//==================================//


void CheckForZigZag()
  {
   ZigZagHandle=iCustom(_Symbol,PERIOD_M1,"Examples\\ZigZag",12,5,3);
   ArraySetAsSeries(ZigZagArray,true);
   CopyBuffer(ZigZagHandle,0,0,100,ZigZagArray);
   int zigzag_counter=0;
   for(int i=0; i<100; i++)
     {
      if(ZigZagArray[i]!=0.0)
        {
         zigzag[zigzag_counter]=ZigZagArray[i];
         zigzag_counter++;
        }
     }
  }
  
  
  
  
  
  
  
  void CheckForZigZagUp2()
  {
   ZigZagHandle2=iCustom(_Symbol,PERIOD_M5,"Examples\\ZigZag",12,5,3);
   ArraySetAsSeries(ZigZagArray2,true);
   CopyBuffer(ZigZagHandle2,0,0,100,ZigZagArray2);
   int zigzag_counter=0;
   for(int i=0; i<100; i++)
     {
      if(ZigZagArray2[i]!=0.0)
        {
         zigzag2[zigzag_counter]=ZigZagArray2[i];
         zigzag_counter++;
        }
     }
  }
  
  
 
  
   
//==================================//
  
 
 
 
 
 
 
 
  
  
  
// Close all order
void closeAllPositions()
  {
   int totalPositions = PositionsTotal(); // Get the total number of open positions
   for(int i = PositionsTotal()- 1; i >= 0; i--)  // Loop through all positions (starting from the most recent)
     {
      ulong ticket = PositionGetTicket(i); // Get the ticket number of the position
      trade.PositionClose(ticket);
      Print("close" + ticket);

     }
  }

//+------------------------------------------------------------------+




