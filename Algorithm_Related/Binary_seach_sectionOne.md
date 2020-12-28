You use while (start <= end) if you are returning the match from inside the loop.
You use while (start < end) if you want to exit out of the loop first, and then use the result of start or end to return the match.
example:
find the minimum of [1], it dosen't need to go into loop;
