# Doge-Gabh
GetProcAddressByHash on Disk

#### add directsyscall from bananaphone


## Functions
```
//getfunc addr by hash
gabh.GetFuncPtr()

//get remap ntdll
gabh.ReMapNtdll()

////get remap func addr
GetFuncUnhook()

//ntdll halo's gate
gabh.NtdllHgate()

//halo's gate call sysid
gabh.HgSyscall()

//get universal ntdll
gabh.Universal()

//get universal func addr
UniversalFindProc()

//full dll unhooking
gabh.FullUnhook()

```

## Usage


```
package main
import (
	"crypto/sha1"
	"crypto/sha256"
	"encoding/hex"
	"fmt"
	gabh "github.com/timwhitez/Doge-Gabh/pkg/Gabh"
	"syscall"
	"unsafe"
)

func main(){
	//
	//	get funcPtr Universal
	//
	ntdll, _ := gabh.Universal(str2sha1)

	//str2sha1(NtDelayExecution)
	sleep, _ := ntdll.UniversalFindProc("84804f99e2c7ab8aee611d256a085cf4879c4be8")

	fmt.Printf("Universal Addr:0x%x\n", sleep)

	fmt.Println("Sleep for 3s")
	times := -(3000 * 10000)
	syscall.Syscall(sleep, 2, 0, uintptr(unsafe.Pointer(&times)), 0)

	//
	//	get funcPtr by hash
	//
	//sha1(sleep)=c3ca5f787365eae0dea86250e27d476406956478
	sleep_ptr,moduleN,err := gabh.GetFuncPtr("kernel32.dll","c3ca5f787365eae0dea86250e27d476406956478",str2sha1)
	if err != nil{
		fmt.Println(err)
		return
	}

	fmt.Printf("%s: %x\n",moduleN,sleep_ptr)
	syscall.Syscall(uintptr(sleep_ptr),1,1000,0,0)

	//sha256(sleep)=d466bcf52eb6921b1e747e51bf2cc1441926455ba146ecc477bed1574e44f9c0
	sleep_ptr,moduleN,err = gabh.GetFuncPtr("kernel32.dll","d466bcf52eb6921b1e747e51bf2cc1441926455ba146ecc477bed1574e44f9c0",Sha256Hex)
	if err != nil{
		fmt.Println(err)
		return
	}

	fmt.Printf("%s: %x\n",moduleN,sleep_ptr)
	syscall.Syscall(uintptr(sleep_ptr),1,1000,0,0)


	//
	//	get unhook ntdll funcPtr by hash
	//
	unNt,e := gabh.ReMapNtdll()
	if e != nil{
		panic(e)
	}
	
	times = -(3000 * 10000)
	//NtDelayExecution
	NtDelayExecution_ptr,_,_ := unNt.GetFuncUnhook("84804f99e2c7ab8aee611d256a085cf4879c4be8",str2sha1)

	fmt.Printf("%s: %x\n","NtDelayExecution ptr ",NtDelayExecution_ptr)
	syscall.Syscall(uintptr(NtDelayExecution_ptr),2,0,uintptr(unsafe.Pointer(&times)),0)


	//
	//	get ntdll hellsgate Sysid by hash
	//
	//NtDelayExecution HellsGate
	sleep1,e := gabh.NtdllHgate("84804f99e2c7ab8aee611d256a085cf4879c4be8",str2sha1)
	if e != nil {
		panic(e)
	}

	fmt.Printf("%s: %x\n","NtDelayExecution Sysid",sleep1)


	//hellsgate syscall
	gabh.HgSyscall(sleep1,0,uintptr(unsafe.Pointer(&times)))

}


func str2sha1(s string) string{
	h := sha1.New()
	h.Write([]byte(s))
	bs := h.Sum(nil)
	return fmt.Sprintf("%x", bs)
}


func Sha256Hex(s string)string{
	return hex.EncodeToString(Sha256([]byte(s)))
}

func Sha256(data []byte)[]byte{
	digest:=sha256.New()
	digest.Write(data)
	return digest.Sum(nil)
}

```

asm_x64.s from https://github.com/C-Sto/BananaPhone


### ref
https://github.com/timwhitez/Doge-ReMap

https://github.com/Binject/debug/

https://github.com/C-Sto/BananaPhone

https://github.com/Binject/universal

https://github.com/trickster0/TartarusGate
