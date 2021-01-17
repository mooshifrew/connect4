using System;
using static System.Console;

namespace Bme121
{
    class Program
    {
        static void Main()
        {
            Connect4 game = new Connect4(7,7);
            bool gameDone = false;
            bool moveValid = false;
            int winner = -1;
            
            game.Display();
            
            while( ! gameDone )
            {
                
                
                while(! moveValid )
                {
                    game.Prompt();
                    moveValid = game.RecieveMove(int.Parse(ReadLine()));
                }
                
                game.Display();
                
                gameDone = game.GameDone();
                
                moveValid = false;
                game.SwitchTurn();
            }
            winner = game.Winner();
            
            if( winner == 0 ) 
                WriteLine("Good job player A");
            else 
                WriteLine("Good job player B");
        }
    }

    class Connect4
    {
        static int Width {get;  set;}
        static int Height {get; set;}
        
        int turn;

        public int[,] board;

        public Connect4(int width, int height)
        {
            turn = 0;
            Width = width;
            Height = height;
            this.board = new int[7,7]; 
            for( int r = 0; r<height; r++)
                for( int c = 0; c<width; c++)
                    this.board[r,c] = -1;
        }
        
        public bool GameDone()
        {
            for( int row = Height-1; row>=0; row-- ) // horizontals
                for( int col = 3; col<Width; col++)
                    if( board[row,col]!= -1 && (board[row,col] == board[row,col-1]) && (board[row,col] == board[row,col-2]) && (board[row,col] == board[row,col-3]) ) 
                        return true;

            for ( int col = 0; col<Width; col++) // verticals
                for( int row = 3; row < Height; row++)
                    if( board[row,col]!= -1 && (board[row,col] == board[row-1,col]) && (board[row,col] == board[row-2,col]) && (board[row,col] == board[row-3,col]) )
                        return true;
            
            for ( int row = Height - 1; row>2; row-- ) // diagonal up right 
                for( int col = 0; col<Width-3; col++)
                    if( board[row,col]!= -1 && (board[row,col]==board[row-1,col+1]) && (board[row,col]==board[row-2,col+2]) && (board[row,col]==board[row-3,col+3]) )
                        return true;
                        
            for( int row = Height-1; row>2; row-- ) // diagonal up left
                for( int col = Width-1; col>2; col--)
                    if( board[row,col]!= -1 && (board[row,col]==board[row-1,col-1]) && (board[row,col]==board[row-2,col-2]) && (board[row,col]==board[row-3,col-3]) )
                        return true;
            
            bool filled = true;;
            for( int row = 0; row<Height; row++)
                for( int col = 0; col<Width; col++)
                    if(board[row,col] == -1 ) filled = false;
            
            return filled;;
        }
        
        public int Winner()
        {
            for( int row = Height-1; row>=0; row-- ) // horizontals
                for( int col = 3; col<Width; col++)
                    if( board[row,col]!= -1 && (board[row,col] == board[row,col-1]) && (board[row,col] == board[row,col-2]) && (board[row,col] == board[row,col-3]) ) 
                        return board[row,col];

            for ( int col = 0; col<Width; col++) // verticals
                for( int row = 3; row < Height; row++)
                    if( board[row,col]!= -1 && (board[row,col] == board[row-1,col]) && (board[row,col] == board[row-2,col]) && (board[row,col] == board[row-3,col]) )
                        return board[row,col];
            
            for ( int row = Height - 1; row>2; row-- ) // diagonal up right 
                for( int col = 0; col<Width-3; col++)
                    if( board[row,col]!= -1 && (board[row,col]==board[row-1,col+1]) && (board[row,col]==board[row-2,col+2]) && (board[row,col]==board[row-3,col+3]) )
                        return board[row,col];
                        
            for( int row = Height-1; row>2; row-- ) // diagonal up left
                for( int col = Width-1; col>2; col--)
                    if( board[row,col]!= -1 && (board[row,col]==board[row-1,col-1]) && (board[row,col]==board[row-2,col-2]) && (board[row,col]==board[row-3,col-3]) )
                        return board[row,col];
            
            
            return -1;
        }
        
        public void SwitchTurn()
        {
            if( turn == 0 ) turn = 1;
            else            turn = 0;
        }
        public void Prompt()
        {   
            if( turn == 0 )
                WriteLine("Player A, enter a column value");
            else 
                WriteLine("Player B, enter a column value");
        }
        
        public bool RecieveMove( int move )
        {
            if( move < 0 || move > Width || board[0,move-1] != -1  )  return false;

            for( int row = Height-1; row>=0; row--)
            {
                if( board[row,move-1] == -1 ) 
                {
                    board[row,move-1] = turn;
                    return true;
                }
            }
            return false;
        }

        public void Display()
        {
            string h  = "\u2500"; // horizontal line
            string v  = "\u2502"; // vertical line
            string tl = "\u250c"; // top left corner
            string tr = "\u2510"; // top right corner
            string bl = "\u2514"; // bottom left corner
            string br = "\u2518"; // bottom right corner
            string vr = "\u251c"; // vertical join from right
            string vl = "\u2524"; // vertical join from left
            string hb = "\u252c"; // horizontal join from below
            string ha = "\u2534"; // horizontal join from above
            string hv = "\u253c"; // horizontal vertical cross

            WriteLine();
            for( int row = 0; row<board.GetLength(0); row++)
            {

                if( row == 0 )
                {
                    for( int col = 0; col<Width; col++)
                        Write("  {0} ", col+1);
                    WriteLine();    
                    for( int col = 0; col<Width; col++) //TopLine
                    {
                        if( col == 0 )
                        {
                            Write("{0}{1}{1}{1}", tl, h);
                        }
                        if( col == Height-1 )
                        {
                            Write("{0}", tr);
                        }
                        else
                        {
                            Write("{0}{1}{1}{1}",hb,h);                            
                        }
                    }
                    WriteLine();
                    for( int col = 0; col<Width; col++) // filling row with values
                    {
                        if( board[row,col]== -1 )
                            Write("{0}   ", v);
                        if( board[row,col]== 0 )
                            Write("{0} A ",v, "A" );
                        if( board[row,col]==1)
                            Write("{0} B ", v);
                    }
                    WriteLine("{0}",v);


                }
                else if( row == board.GetLength(0)-1 ) // last row
                {
                    for( int col = 0; col<Width; col++) //frame above
                    {
                        if( col == 0 )
                            Write("{0}{1}{1}{1}", vr, h);
                        if( col == Width-1 )
                            Write("{0}", vl);
                        else
                            Write("{0}{1}{1}{1}",hv, h);
                    }
                    WriteLine();
                    for( int col = 0; col<Width; col++) // filling row with values
                        {
                            if( board[row,col]== -1 )
                                Write("{0}   ", v);
                            if( board[row,col]== 0 )
                                Write("{0} A ",v, "A" );
                            if( board[row,col]==1)
                                Write("{0} B ", v);
                        }
                    WriteLine("{0}",v);
                    for( int col = 0; col<Width; col++)
                    {
                        if( col == 0 )
                            Write("{0}{1}{1}{1}", bl, h);
                        if( col == Width-1) 
                            Write("{0}", br);
                        else
                            Write("{0}{1}{1}{1}", ha, h);
                    }
                    WriteLine();
                }
                else // normal rows
                {
                    for( int col = 0; col<Width; col++) //frame above
                    {
                        if( col == 0 )
                            Write("{0}{1}{1}{1}", vr, h);
                        if( col == Width-1 )
                            Write("{0}", vl);
                        else
                            Write("{0}{1}{1}{1}",hv, h);
                    }
                    WriteLine();
                    for( int col = 0; col<Width; col++) // filling row with values
                        {
                            if( board[row,col]== -1 )
                                Write("{0}   ", v);
                            if( board[row,col]== 0 )
                                Write("{0} A ",v, "A" );
                            if( board[row,col]==1)
                                Write("{0} B ", v);
                        }
                    WriteLine("{0}",v);


                }
                
                

            }
            
        }


        
    }
}
