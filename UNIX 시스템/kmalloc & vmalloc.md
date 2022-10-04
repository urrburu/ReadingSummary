### 1. Page Allocator

다음은 page allocator를 이용하여 페이지 단위로 메모리를 할당하는 API들이다.

```c
static inline void *page_address(const struct page *page);
 
static inline void set_page_address(struct page *page, void *address);
 
static inline struct page *
alloc_pages(gfp_t gfp_mask, unsigned int order);
 
#define alloc_page(gfp_mask) alloc_pages(gfp_mask, 0)
 
unsigned long __get_free_pages(gfp_t gfp_mask, unsigned int order);
 
unsigned long get_zeroed_page(gfp_t gfp_mask);
```

page_address는 인자로 전달받은 page의 가상(논리) 주소를 반환한다.

set_page_address는 인자로 전달받은 page의 가상 주소를 address로 설정한다.

alloc_pages는 2^order개 만큼의 페이지를 할당한다.

alloc_page는 alloc_pages의 order에 0을 전달하여 페이지 하나를 할당한다.

__get_free_pages는 내부적으로 alloc_pages를 호출하며, HIGHMEM 영역에 할당받지 않도록 마스킹과 메모리 할당이 실패했을 경우의 예외처리를 포함하고 있다.

get_zeroed_page는 __get_free_pages의 order에 0을 전달하여 페이지 하나를 할당한다.

HIGHMEM 영역에 메모리를 할당하는 것이 아니면 페이지 할당에는 __get_free_pages 함수를 사용하는 것이 좋다.

아무튼 위의 할당 함수들은 결국 내부적으로 alloc_pages를 호출한다.

alloc_pages는 alloc_pages_current를 호출하며, 해당 함수는 정책에 따라 alloc_page_interleave나 __alloc_pages_nodemask를 호출한다.

alloc_page_interleave는 __alloc_pages를 호출하고, 이 함수는 다시 __alloc_pages_nodemask를 호출한다.

반대로 할당한 페이지를 반환하는 함수들의 정의는 다음과 같다.

```c
void free_pages(unsigned long addr, unsigned int order)
{
	if (addr != 0) {
		VM_BUG_ON(!virt_addr_valid((void *)addr));
		__free_pages(virt_to_page((void *)addr), order);
	}
}
 
#define free_page(addr) free_pages((addr), 0)
 
void __free_pages(struct page *page, unsigned int order)
{
	if (put_page_testzero(page))
		free_the_page(page, order);
}
```

보는 것과 같이 결국 __free_pages를 호출한다.

실 사용은 예외처리가 되어있는 free_pages를 사용하는 것이 좋다.

### 2. kmalloc

kmalloc 함수의 프로토타입은 다음과 같다.

```c
/**
 * kmalloc - allocate memory
 * @size: how many bytes of memory are required.
 * @flags: the type of memory to allocate.
 *
 * kmalloc is the normal method of allocating memory
 * for objects smaller than page size in the kernel.
 *
 * The allocated object address is aligned to at least ARCH_KMALLOC_MINALIGN
 * bytes. For @size of power of two bytes, the alignment is also guaranteed
 * to be at least to the size.
 *
 * The @flags argument may be one of the GFP flags defined at
 * include/linux/gfp.h and described at
 * :ref:`Documentation/core-api/mm-api.rst <mm-api-gfp-flags>`
 *
 * The recommended usage of the @flags is described at
 * :ref:`Documentation/core-api/memory-allocation.rst <memory-allocation>`
 *
 * Below is a brief outline of the most useful GFP flags
 *
 * %GFP_KERNEL
 *	Allocate normal kernel ram. May sleep.
 *
 * %GFP_NOWAIT
 *	Allocation will not sleep.
 *
 * %GFP_ATOMIC
 *	Allocation will not sleep.  May use emergency pools.
 *
 * %GFP_HIGHUSER
 *	Allocate memory from high memory on behalf of user.
 *
 * Also it is possible to set different flags by OR'ing
 * in one or more of the following additional @flags:
 *
 * %__GFP_HIGH
 *	This allocation has high priority and may use emergency pools.
 *
 * %__GFP_NOFAIL
 *	Indicate that this allocation is in no way allowed to fail
 *	(think twice before using).
 *
 * %__GFP_NORETRY
 *	If memory is not immediately available,
 *	then give up at once.
 *
 * %__GFP_NOWARN
 *	If allocation fails, don't issue any warnings.
 *
 * %__GFP_RETRY_MAYFAIL
 *	Try really hard to succeed the allocation but fail
 *	eventually.
 */
static __always_inline void *kmalloc(size_t size, gfp_t flags);
```

kmalloc 함수는 커널 영역에 연속된 물리 메모리를 할당하기 위해 사용한다.

kmalloc 함수는 내부적으로 __kmalloc을 호출하고 다시 __do_kmalloc을 호출한다.

(사이즈에 따라 kmalloc_large 등을 호출할 수도 있다.)

__do_kmalloc은 다시 slab_alloc을 호출한다.

slab_alloc은 slab system에서는 __do_cache_alloc -> ____cache_alloc을 호출하며,

slub system에서는 slab_alloc_node를 호출한다. (최신 시스템 및 커널이라면 아마 slub을 사용할 것이다.)

____cache_alloc나 slab_alloc_node 함수는 추후에 알아볼 slab 할당자를 통해 캐시에 메모리를 할당한다.

이렇게 kmalloc 함수로 할당한 메모리는 kfree 함수로 반환한다.

```c
/**
 * kfree - free previously allocated memory
 * @objp: pointer returned by kmalloc.
 *
 * If @objp is NULL, no operation is performed.
 *
 * Don't free memory not originally allocated by kmalloc()
 * or you will run into trouble.
 */
void kfree(const void *objp);
```

kfree는 내부에서 __cache_free -> ___cache_free를 호출하여 메모리를 반환한다.

다음은 간단한 kmalloc 사용 예제이다.

```c
#include <linux/init.h>
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/slab.h>
 
static char *buffer = NULL;
 
static int __init kmalloc_init(void)
{
	if ((buffer = kmalloc(1024, GFP_KERNEL)) != NULL)
		printk("Memory Allocation\n");
		return 0;
}
 
static void __exit kmalloc_exit(void)
{
	if (buffer != NULL)
		kfree(buffer);
}
 
module_init(kmalloc_init);
module_exit(kmalloc_exit);
MODULE_LICENSE("GPL");
```

### 3. vmalloc

vmalloc 함수의 프로토타입은 다음과 같다.

```c
/**
 * vmalloc - allocate virtually contiguous memory
 * @size:    allocation size
 *
 * Allocate enough pages to cover @size from the page level
 * allocator and map them into contiguous kernel virtual space.
 *
 * For tight control over page level allocator and protection flags
 * use __vmalloc() instead.
 *
 * Return: pointer to the allocated memory or %NULL on error
 */
void *vmalloc(unsigned long size);
```

vmalloc 함수는 큰 메모리 공간이 필요할 때 사용한다.

이 함수로 할당한 메모리는 가상 메모리 공간에서는 연속적이지만 물리 메모리 공간에서는 연속적이지 않다.

vmalloc은 내부적으로 __vmalloc_node_flags -> __vmalloc_node -> __vmalloc_node_range -> __get_vm_area_node를 호출한다.

__get_vm_area_node는 kzalloc_node로 물리 메모리를 할당하고 alloc_vmap_area로 가상 공간을 할당받아서 setup_vmalloc_vm -> setup_vmalloc_vm_locked 함수로 할당받은 가상 메모리와 물리 메모리를 연결한다.

이렇게 vmalloc으로 할당한 메모리는 vfree로 반환한다.

```c
/**
 * vfree - release memory allocated by vmalloc()
 * @addr:  memory base address
 *
 * Free the virtually continuous memory area starting at @addr, as
 * obtained from vmalloc(), vmalloc_32() or __vmalloc(). If @addr is
 * NULL, no operation is performed.
 *
 * Must not be called in NMI context (strictly speaking, only if we don't
 * have CONFIG_ARCH_HAVE_NMI_SAFE_CMPXCHG, but making the calling
 * conventions for vfree() arch-depenedent would be a really bad idea)
 *
 * May sleep if called *not* from interrupt context.
 *
 * NOTE: assumes that the object at @addr has a size >= sizeof(llist_node)
 */
void vfree(const void *addr);
```

vfree는 내부에서 __vfree -> __vunmap을 호출한다.

__vunmap 함수는 vm_remove_mappings로 매핑을 해제한 후, __free_pages, kvfree로 할당받은 페이지를 반환하며 kfree 를 호출하여 물리 메모리를 반환한다.

다음은 간단한 vmalloc 사용 예제이다.

```c
#include <linux/init.h>
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/vmalloc.h>
 
static char *buffer = NULL;
 
static int __init vmalloc_init(void)
{
	if ((buffer = vmalloc(10 * PAGE_SIZE)) != NULL)
		memset(buffer, 0, 10 * PAGE_SIZE);
		printk("Memory Allocation\n");
		return 0;
}
 
static void __exit vmalloc_exit(void)
{
	if (buffer != NULL)
		vfree(buffer);
}
 
module_init(vmalloc_init);
module_exit(vmalloc_exit);
MODULE_LICENSE("GPL");
```

## kmalloc 함수는 연속적인 물리 메모리를 할당하며 vmalloc 보다 빠르기 때문에 일반적으로 해당 함수를 사용한다.

## vmalloc 함수는 연속적인 가상 메모리를 할당하며 큰 사이즈의 메모리를 할당할 때 사용한다.

