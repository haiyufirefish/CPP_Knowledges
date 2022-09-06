The **n-queens** puzzle is the problem of placing `n` queens on an `n x n` chessboard such that no two queens attack each other.

Given an integer `n`, return *all distinct solutions to the **n-queens puzzle***. You may return the answer in **any order**.

Each solution contains a distinct board configuration of the n-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space, respectively.



```c++
class Solution {
private:
    bool isValid(vector<string> &board,int row, int col){
        // this column
        for(int i = 0;i<board.size();++i){
            if(board[row][i] == 'Q')return false;
        }
        
        //left below
        for(int i = row +1,j = col -1;i<board.size()&&j>=0;++i,--j){
            if(board[i][j]=='Q')return false;
        }
        //left above
        for(int i = row -1,j = col -1;i>=0&&j>=0;--i,--j){
            if(board[i][j]=='Q')return false;
        }
        
        return true;
    }

    
    void backtrack(vector<string> &board,vector<vector<string>> &res,int col){
        if(board.size() == col){
            res.push_back(board);
            return;
        }
        int n = board.size();
        for(int row = 0;row < n;++row){
            if(!isValid(board,row,col))continue;
            board[row][col] = 'Q';
            
            backtrack(board,res,col+1);
            board[row][col] = '.';
        }
    }
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans;
        vector<string>board(n,string(n,'.'));
        backtrack(board,ans,0);
        return ans;
    }
};
```

backtrack: left above row, right above row.  col+1, for 0.. row -1;