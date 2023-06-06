import struct 
import socket 
import threading


"""
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
"""

# Config 
src_ip = "127.0.0.1"
dst_ip = "127.0.0.1"
dst_port = 65535
payloads = b"hello" 

laddr = (src_ip, dst_port)


def checksum(data):
    if len(data) % 2 != 0:
        data += b'\x00'

    n = 0
    for i in range(0, len(data), 2):
        word = struct.unpack("!H", data[i:i+2])[0]
        n += word
    
    resolve = (n >> 16) + (n & 0xffff)
    resolve += resolve
    
    resolve = ~resolve & 0xffff
    resolve -= 1
    
    return resolve


def udp_checksum_with_payload(src_ip:str, dst_ip:str, payloads:bytes) -> bytes:
    
    udp_length = 11 + len(payloads)  # UDPヘッダとペイロードの長さ
    
    # 擬似ヘッダーを作成
    udp_pseudoheader  = struct.pack('!4s4sBBH', socket.inet_aton(src_ip),
            socket.inet_aton(dst_ip), 0, 17, udp_length)
    # パケットを作成
    udp_packet = udp_pseudoheader + payloads
    # チェックサムを計算
    checksum_n = checksum(udp_packet)
    
    print(checksum_n)

    # チェックサム値をペイロードに仕込む
    checksums_with_payloads = struct.pack("!Hx", checksum_n) + payloads
     
    return checksums_with_payloads
    


def udp_checksum_with_sendto(src_ip:str, dst_ip:str, dst_port:int, payloads:bytes):
    
    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    
    # チェックサム付きペイロードを作成
    checksum_with_paylaods = udp_checksum_with_payload(src_ip, dst_ip, payloads)
    dst_addr = (dst_ip, dst_port)
    
    s.sendto(checksum_with_paylaods, dst_addr)
    

def udp_checksum_with_recv():
    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    
    try:
        s.bind(laddr)
    except Exception as e:
        print("e") 
        return -1 

    while True:
        data, addr = s.recvfrom(256)
         
        get_checksum = struct.unpack("!Hx", data[:3])[0]
        getsockname  = s.getsockname()
        get_payloads = data[3:]
        get_len      = 11 + len(get_payloads)

        rhost = addr[0]
        lhost = getsockname[0]
         
        udp_pseudoheader = struct.pack('!4s4sBBH', socket.inet_aton(rhost),
            socket.inet_aton(lhost), 0, 17, get_len) 
        
        udp_packet = udp_pseudoheader + get_payloads

        """
          If a packet is tampered with or missing, 
          the checksum calculation results will be 
          different and the problem can be detected.
        """
        checksum_calculated = checksum(udp_packet)
        
        # Compare checksum
        if get_checksum == checksum_calculated:
            print("checksum match")
            
        else:
            print("checksum mismatch")
            

if __name__ == "__main__":
    
    # client A
    server_th = threading.Thread(target=udp_checksum_with_recv).start()
    
    # client B 
    udp_checksum_with_sendto(src_ip, dst_ip, dst_port, payloads)
    
    
