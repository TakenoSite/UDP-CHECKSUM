# UDP-CHECKSUM

The Simple DGRAM Checksum

      
    RFC 793 
                     RANSMISSION CONTROL PROTOCOL

                         DARPA INTERNET PROGRAM

                         PROTOCOL SPECIFICATION


    Checksum:  16 bits

    The checksum field is the 16 bit one's complement of the one's
    complement sum of all 16 bit words in the header and text.  If a
    segment contains an odd number of header and text octets to be
    checksummed, the last octet is padded on the right with zeros to
    form a 16 bit word for checksum purposes.  The pad is not
    transmitted as part of the segment.  While computing the checksum,
    the checksum field itself is replaced with zeros.

    The checksum also covers a 96 bit pseudo header conceptually

    
    

    unsigned short checksum(unsigned short *ptr , int size)  
    {  
        int index         = 0;  
        int sum           = 0;  
        process_controle  = 0; // swich true
        for (index = 0;index<size;index+=2){  
                sum += *(ptr++);  
        }  
        short int carry = sum >> 16;  
        short int main = 0x0000ffff & sum;
        short int result = ~(main+carry);  
        return result;  
    };
    
